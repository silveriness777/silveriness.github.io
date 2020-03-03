---
layout: post
title: 'Laravel/Lumen 5.6后自定义错误日志格式过滤堆栈信息'
category: PHP
tags: PHP
keywords: PHP Laravel Laravel5.6 Laravel5.8 laravel异常日志堆栈
excerpt: "Laravel/Lumen的日志默认是基于Monolog进行的一层封装，简单使用的话文档还是很清晰的。但是当我希望过滤错误日志里面的大量堆栈时候，以及一些其他自定义配置时，发现5.6版本之后对日志系统做了升级。原有的configureMonologUsing自定义Monolog设置已经废弃。参照新的文档和源码，重新做了封装后，基本解决异常日志堆栈问题。
"
---

## 目录
### 概述

Laravel/Lumen的日志默认是基于Monolog进行的一层封装，简单使用的话文档还是很清晰的。

但是当我希望过滤错误日志里面的大量堆栈时候，以及一些其他自定义配置时，发现5.6版本之后对日志系统做了升级。原有的configureMonologUsing自定义Monolog设置已经废弃。参照新的文档和源码，重新做了封装后，基本解决异常日志堆栈问题。

### 构建自定义日志通道
所有的应用程序日志系统配置都位于 config/logging.php 配置文件中。这个文件允许你配置你的应用程序日志通道，所以务必查看每个可用的通道及它们的选项。

lumen 框架下该配置文件可以在vendor包中找到对应的配置模板文件，复制到config下，同时需要在bootstrap/app.php中注册。

```php
$app->configure($config);
```

针对具体的配置信息简单的在下文demo说明下：

```
<?php

// 配置文件路径：/config/logging.php

return [

    // 默认用哪个
    'default' => env('LOG_CHANNEL', 'stack'),

    'channels' => [
        //自定义频道
        'myapplog' => [
            // 日志驱动模式：
            'driver' => 'daily',                           
            // 日志存放路径
            'path' => storage_path('logs/myapplog.log'),
            // 日志等级：
            'level' => 'info',
            // 日志分片周期，多少天一个文件
            'days' => 1,
        ],
    ],
];
```

#### 日志使用
```
<?php
use Log;

class LogTestController extends Controller
{
  $message = 'Some message';
  $log = ['site_name'=>'studytime.xin','site_id'=>'1']; 
  Log::channel('myapplog')->info($message, $log);  //Log后的数组会自动转成Json存到日志记录中
}
```

#### 日志写入结果
```
[2020-03-01 10:22:28] local.INFO: Some message {'site_name':'studytime.xin','site_id':'1'}
```

### 高度自定义 Monolog 通道
有时需要完全控制已存在通道的 Monolog： 比如，你可能想要为给定通道的日志处理配置自定义的 Monolog FormatterInterface 实现：

先在通道配置中定义一个 tap 数组。 tap 数组包含一个在通道创建后有机会用于自定义 Monolog 实例的类列表：
```
'single' => [
    'driver' => 'single',
    'tap' => [App\Logging\CustomizeFormatter::class],
    'path' => storage_path('logs/laravel.log'),
    'level' => 'debug',
],
```

一旦在通道中有了 tap 选项配置，就要准备用于自定义 Monolog 实例的类。这种类这需要一个方法： __invoke，它接受一个 Illuminate\Log\Logger 实例作为其参数。 Illuminate\Log\Logger 实例将所有方法调用代理到基础的 Monolog 实例：
```
<?php

namespace App\Logging;

class CustomizeFormatter
{
    /**
     * 自定义给定的日志实例。
     *
     * @param  \Illuminate\Log\Logger  $logger
     * @return void
     */
    public function __invoke($logger)
    {
        foreach ($logger->getHandlers() as $handler) {
            $handler->setFormatter(...);
        }
    }
}
```
Tip：所有的 "tap" 类都是由 服务容器 解析的，因此任何依赖它们的构造器都会自动被注入。这段算是官方文档的说明了。


### 如何实现自定义错误日志格式、过滤大量异常日志堆栈 

#### 在 config/logging.php 创建对应的处理渠道
```
'channels' => [
    'custom' => [
        'driver' => 'daily',
        'path' => storage_path('logs/lumen.log'),
        'tap' => [App\Logging\MyLogFormatter::class],
        'days' => 1,
        'level' => 'info', 
    ],
],
```

#### 在 app/Logging 目录下创建 MyLogFormatter 自定义 Monolog 实例的类

```php
<?php

namespace App\Logging;

class MyLogFormatter
{
    public function __invoke($logger)
    {
        foreach ($logger->getHandlers() as $handler) {
            $handler->setFormatter(new LineFormatter());
        }
    }
}
```

#### 在 app/Logging 目录下创建 用于重写 Monolog 的 LineFormatter 格式化处理器
LineFormatter 是 Monolog 默认的格式化处理器

```php
<?php


namespace App\Logging;

use Illuminate\Support\Str;
use Monolog\Formatter\LineFormatter as BaseLineFormatter;
use Monolog\Formatter\NormalizerFormatter;

class LineFormatter extends BaseLineFormatter
{
    const NEW_SIMPLE_FORMAT = "[%datetime%] [%uuid%] %channel%.%level_name%: %message% %context% %extra%\n";

    public function format(array $record)
    {
        $output = self::NEW_SIMPLE_FORMAT;
        $vars   = (new NormalizerFormatter())->format($record);
        $vars['uuid'] = 'uuid:' . Str::uuid();
        foreach ($vars['extra'] as $var => $val) {
            if (false !== strpos($output, '%extra.' . $var . '%')) {
                $output = str_replace('%extra.' . $var . '%', $this->stringify($val), $output);
                unset($vars['extra'][$var]);
            }
        }
        if (isset($vars['context']['exception']) && !empty($vars['context']['exception'])) {
            $vars['message'] = '';
            $vars['context'] = $vars['context']['exception'];
            if (isset($vars['context']['trace'])) {
                unset($vars['context']['trace']);
            }
            if (isset($vars['context']['previous'])) {
                unset($vars['context']['previous']);
            }
        }

        if (false !== strpos($output, '%')) {
            $output = preg_replace('/%(?:extra|context)\..+?%/', '', $output);
        }

        foreach ($vars as $var => $val) {
            if (false !== strpos($output, '%' . $var . '%')) {
                $output = str_replace('%' . $var . '%', $this->stringify($val), $output);
            }
        }
        // remove leftover %extra.xxx% and %context.xxx% if any
        if (false !== strpos($output, '%')) {
            $output = preg_replace('/%(?:extra|context)\..+?%/', '', $output);
        }
        return $output;
    }
}
```

#### 异常日志处理效果
```
[2020-03-03 21:42:36] [uuid:374b0045-7aef-4d4b-9ba0-fce13d8dd776] local.ERROR:  {"class":"Illuminate\\Queue\\MaxAttemptsExceededException","message":"App\\Jobs\\DownloadReportDataJob has been attempted too many times or run too long. The job may have previously timed out.","code":0,"file":"/data/wwwroot/advertisingscript-line/vendor/illuminate/queue/Worker.php:601"} []
```
此时可以看到异常的日志已经没有大量的堆栈问题。



---
layout: post
title: lumen 记录sql日志方法
category: Laravel 
tags: [Laravel lumen]
excerpt: "研发过程中有时会需要查看sql情况，记录下怎么记录sql日志"
---


## 目录

### 在 Listeners 目录新建 QueryListener.php 文件 代码如下：
```
<?php

namespace App\Listeners;

use Illuminate\Database\Events\QueryExecuted;
use Monolog\Handler\RotatingFileHandler;
use Monolog\Logger;

/**
 * Class QueryListener
 * @package App\Listeners
 */
class QueryListener
{
    public function handle(QueryExecuted $event)
    {
        if (config('app.debug') == true) {
            $sql = str_replace('?', "'%s'", str_replace('%', ord('~'), $event->sql));
            $url = isset($_REQUEST['REQUEST_URI']) ? $_REQUEST['REQUEST_URI'] : '';
            $log = 'url:' . $url . ', ' . '[sql]:' . str_replace(ord('~'), '%', vsprintf($sql, $event->bindings)) . 'time is ' . ($event->time / 1000) . 's';
            (new Logger('sql'))->pushHandler(new RotatingFileHandler(storage_path('logs/sql/' . date('Y-m-d') . '.log')))->info($log);
        }
    }
}

```


### 在 Providers/EventServiceProvider.php 添加如下代码:
```
<?php

namespace App\Providers;

use Laravel\Lumen\Providers\EventServiceProvider as ServiceProvider;

class EventServiceProvider extends ServiceProvider
{
    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [

        'Illuminate\Database\Events\QueryExecuted' => [
            'App\Listeners\QueryListener'
        ],
    ];
}


```


### 在 bootstrap/app.php 文件中开启 EventServiceProvider 注册
```
$app->register(App\Providers\EventServiceProvider::class);
```

### 执行验证
sql执行后会在项目storage/logs目录下生成sql

https://blog.csdn.net/m0sh1/article/details/79257935

以我开发的zero/lumen-elasticsearch包为例

1、创建开发包文件E:\www\cuobian\api\packages
注意这里的packages这个文件名可以自定义但是一定要和app目录同级

.
├── apidoc.json
├── app
├── artisan
├── bootstrap
├── composer.json
├── composer.lock
├── composer.phar
├── config
├── database
├── _ide_helper.php
├── package.json
├── packages
├── phpunit.xml
├── public
├── README.md
├── resources
├── storage
├── tests
└── vendor

2、创建开发包文件结构E:\www\cuobian\api\packages\zero\lumen-elasticsearch\src


cd /www/cuobian/api/packages

mkdir -p zero/lumen-elasticsearch/src


3.初始化composer.json文件会有一些提示，按需要填写
cd /www/cuobian/api/packages/zero/lumen-elasticsearch
composer init
文件结构如下：

├── composer.json
└── src

4.修改E:\www\cuobian\api\composer.json文件
"autoload": {
    "psr-4": {
        "App\\": "app/",
        "Zero\\LumenElasticsearch\\": "packages/zero/lumen-elasticsearch/src"
    }
},
5.在E:\www\cuobian\api\packages\zero\lumen-elasticsearch\src下创建文件

结构如下：
├── composer.json
└── src
    ├── Facade.php
    ├── Factory.php
    ├── LumenManager.php
    └── LumenServiceProvider.php

6.在E:\www\cuobian\api\bootstrap\app.php 注册服务

//引入elasticsearch
$app->register(Zero\LumenElasticsearch\LumenServiceProvider::class);
//class_alias('Cviebrock\LaravelElasticsearch\Facade', 'Elasticsearch');
$app->configure('elasticsearch');//必须配置否则会报错
7.在自己对应的本地环境设置配置文件，我本地就是
E:\www\cuobian\api\config\develop\elasticsearch.php

<?php

return [

    /**
     * You can specify one of several different connections when building an
     * Elasticsearch client.
     *
     * Here you may specify which of the connections below you wish to use
     * as your default connection when building an client. Of course you may
     * use create several clients at once, each with different configurations.
     */

    'defaultConnection' => 'default',

    /**
     * These are the connection parameters used when building a client.
     */

    'connections' => [

        'default' => [

            /**
             * Hosts
             *
             * This is an array of hosts that the client will connect to. It can be a
             * single host, or an array if you are running a cluster of Elasticsearch
             * instances.
             */

            'hosts' => [
                [
                    'host'   => env('ELASTICSEARCH_HOST', 'localhost'),
                    'port'   => env('ELASTICSEARCH_PORT', 9200),
                    'scheme' => env('ELASTICSEARCH_SCHEME', null),
                    'user'   => env('ELASTICSEARCH_USER', null),
                    'pass'   => env('ELASTICSEARCH_PASS', null),
                ],
            ],

            /**
             * SSL
             */

            'sslVerification' => null,

            /**
             * Logging
             *
             * Logging is handled by passing in an instance of Monolog\Logger (which
             * coincidentally is what Laravel's default logger is).
             */

            'logging' => false,

            // If you have an existing instance of Monolog you can use it here.
            // 'logObject' => \Log::getMonolog(),

            'logPath' => storage_path('logs/elasticsearch.log'),

            'logLevel' => Monolog\Logger::INFO,

            /**
             * Retries
             */

            'retries' => null,

          

            'sniffOnStart' => false,

            /**
             * HTTP Handler
             */

            'httpHandler' => null,


            'connectionPool' => null,


            'connectionSelector' => null,


            'serializer' => null,

            /**
             * Connection Factory
             */

            'connectionFactory' => null,

            /**
             * Endpoint
             */

            'endpoint' => null,

        ],

    ],

];


8.在E:\www\cuobian\api目录下执行，重新生成自动加载文件配置
composer dump-autoload








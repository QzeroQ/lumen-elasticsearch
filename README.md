## 以我开发的zero/lumen-elasticsearch包为例
###注意本插件使用的是lumen框架
####1.安装依赖
```shell
composer require zzq/lumen-elasticsearch
```
####2.创建elasticsearch.php配置文件，文件内容如下：
```php
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
             *
             * This is the only configuration value that is mandatory.
             *
             * Presently using "extended" host configuration method
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#_extended_host_configuration
             *
             * There is also the shorter "inline" configuration method available
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#_inline_host_configuration
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
             *
             * If your Elasticsearch instance uses an out-dated or self-signed SSL
             * certificate, you will need to pass in the certificate bundle.  This can
             * either be the path to the certificate file (for self-signed certs), or a
             * package like https://github.com/Kdyby/CurlCaBundle.  See the documentation
             * below for all the details.
             *
             * If you are using SSL instances, and the certificates are up-to-date and
             * signed by a public certificate authority, then you can leave this null and
             * just use "https" in the host path(s) above and you should be fine.
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_security.html#_ssl_encryption_2
             */

            'sslVerification' => null,

            /**
             * Logging
             *
             * Logging is handled by passing in an instance of Monolog\Logger (which
             * coincidentally is what Laravel's default logger is).
             *
             * If logging is enabled, you either need to set the path and log level
             * (some defaults are given for you below), or you can use a custom logger by
             * setting 'logObject' to an instance of Psr\Log\LoggerInterface.  In fact,
             * if you just want to use the default Laravel logger, then set 'logObject'
             * to \Log::getMonolog().
             *
             * Note: 'logObject' takes precedent over 'logPath'/'logLevel', so set
             * 'logObject' null if you just want file-based logging to a custom path.
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#enabling_logger
             */

            'logging' => false,

            // If you have an existing instance of Monolog you can use it here.
            // 'logObject' => \Log::getMonolog(),

            'logPath' => storage_path('logs/elasticsearch.log'),

            'logLevel' => Monolog\Logger::INFO,

            /**
             * Retries
             *
             * By default, the client will retry n times, where n = number of nodes in
             * your cluster. If you would like to disable retries, or change the number,
             * you can do so here.
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#_set_retries
             */

            'retries' => null,

            /**
             * The remainder of the configuration options can almost always be left
             * as-is unless you have specific reasons to change them.  Refer to the
             * appropriate sections in the Elasticsearch documentation for what each option
             * does and what values it expects.
             */

            /**
             * Sniff On Start
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html
             */

            'sniffOnStart' => false,

            /**
             * HTTP Handler
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#_configure_the_http_handler
             * @see http://ringphp.readthedocs.org/en/latest/client_handlers.html
             */

            'httpHandler' => null,

            /**
             * Connection Pool
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#_setting_the_connection_pool
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_connection_pool.html
             */

            'connectionPool' => null,

            /**
             * Connection Selector
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#_setting_the_connection_selector
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_selectors.html
             */

            'connectionSelector' => null,

            /**
             * Serializer
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#_setting_the_serializer
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_serializers.html
             */

            'serializer' => null,

            /**
             * Connection Factory
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#_setting_a_custom_connectionfactory
             */

            'connectionFactory' => null,

            /**
             * Endpoint
             *
             * @see https://www.elastic.co/guide/en/elasticsearch/client/php-api/2.0/_configuration.html#_set_the_endpoint_closure
             */

            'endpoint' => null,

        ],

    ],

];


```

#####注意：该elasticsearch.php配置文件的放置路径，应该跟你项目的database.php配置文件同级（这句话如果不理解的话，可以给我发邮件询问，zzqzziavip@163.com）


####3.lumen中注册服务
修改project/bootstrap/app.php

```php
//引入elasticsearch
$app->register(Zero\LumenElasticsearch\LumenServiceProvider::class);
//class_alias('Cviebrock\LaravelElasticsearch\Facade', 'Elasticsearch');
$app->configure('elasticsearch');//必须配置否则会报错
```
####4.使用示例：
```php
$data = [
    'body' => [
        'testField' => 'aaa'
    ],
    'index' => 'my_index',
    'type' => 'my_type',
    'id' => 'my_id',
];

$client = ClientBuilder::create()->build();
$return = $client->index($data);
#You can now replace those last two lines with simply:
$return = Elasticsearch::index($data);


#To grab statistics for an index:

$stats = Elasticsearch::indices()->stats(['index' => 'my_index']);
$stats = Elasticsearch::nodes()->stats();
$stats = Elasticsearch::cluster()->stats();
#To create and restore snapshots (read the Elastic docs about creating repository paths and plugins first):

$response = Elasticsearch::snapshots()->create($params);
$response = Elasticsearch::snapshots()->restore($params);
#To delete whole indices (be careful!):

$response = Elasticsearch::indices()->delete(['index' => 'my_index']);
```

Name
====

openwaf_api 是 [openwaf](https://github.com/titansec/openwaf) 的子模块，用于控制读取/修改内存中的部分信息

TODO
====

* 支持删除指定策略的统计信息

Table of Contents
=================

* [access_rule](#access_rule)
* [policy](#policy)
* [stat](#stat)
* [version](#version)
* [shm](#shm)
* [timer_count](#timer_count)
* [engine_info](#engine_info)

access_rule
===========

* [GET](#access_rule_get)
* [POST](#access_rule_post)
* [PUT](#access_rule_put)
* [DELETE](#access_rule_delete)

<a id="access_rule_get" name="access_rule_get">GET</a>
---

* API: GET /webapadmin/access_rule/{user}/{uuid}

    功能：查询某用户下某条接入规则

* API: GET /webapadmin/access_rule

    功能：查询所有用户下所有接入规则

* API: GET /webapadmin/access_rule/{user}

    功能：查询某用户下所有接入规则

    注：user 为用户 ID, uuid 为接入规则 ID

[Back to TOC](#table-of-contents)

<a id="access_rule_post" name="access_rule_post">POST</a>
---

* API: POST /webapadmin/access_rule/{user}/{pos}

    功能：按索引添加接入规则。若 pos 为空时，表示添加至 user 接入规则最后一条

    注：user 为用户 ID，pos 为位置索引

* API: POST /webapadmin/access_rule/{user}/uuid/{uuid}

    功能：按 uuid 添加接入规则。新增规则会添加至对应 uuid 的下一条规则，若 uuid 为空，则添加至 user 接入规则第一条

    注: user 为用户 ID，uuid 为接入规则 ID

```
    例: 初始接入规则如下：
    {
        "twaf_access_rule": {
            "rules":[
                {"user":"aa","uuid":"aa"},
                {"user":"bb","uuid":"bb"}
            ]
        }
    }

    在用户 aa 中，添加一条接入规则，且排序为 2
    curl http://127.0.0.1:61111/webapadmin/access_rule/aa/2 -X POST -d '{"config":{"user":"aa","uuid":"aaa"}}'

    在用户 aa 中，添加一条接入规则，且排序为 1
    curl http://127.0.0.1:61111/webapadmin/access_rule/aa/1 -X POST -d '{"config":{"user":"aa","uuid":"aaaa"}}'

    在用户 bb 中，添加一条接入规则，且排序为末尾
    curl http://127.0.0.1:61111/webapadmin/access_rule/bb -X POST -d '{"config":{"user":"bb","uuid":"bbb"}}'

    在用户 bb 中，添加一条接入规则，且添加至 uuid 为 bbb 的下一条
    curl http://127.0.0.1:61111/webapadmin/access_rule/bb/uuid/bbb -X POST -d '{"config":{"user":"bb","uuid":"bbbb"}}'

    在用户 bb 中，添加一条接入规则，且添加至第一条
    curl http://127.0.0.1:61111/webapadmin/access_rule/bb/uuid -X POST -d '{"config":{"user":"bb","uuid":"b"}}'

    接入规则结果如下：
    {
        "twaf_access_rule": {
            "rules":[
                {"user":"aa","uuid":"aaaa"},
                {"user":"aa","uuid":"aa"},
                {"user":"aa","uuid":"aaa"},
                {"user":"bb","uuid":"b"},
                {"user":"bb","uuid":"bb"},
                {"user":"bb","uuid":"bbb"},
                {"user":"bb","uuid":"bbbb"}
            ]
        }
    }

    接入规则添加成功
```

[Back to TOC](#table-of-contents)


<a id="access_rule_put" name="access_rule_put">PUT</a>
---

* API: PUT /webapadmin/access_rule/{user}/{uuid}

    功能：修改已有接入规则

    注：user 为用户 ID, uuid 为接入规则 ID

```
    例：当前接入规则如下：
    {
        "twaf_access_rule": {
            "rules":[
                {"user":"aa","uuid":"aa", "port":1},
                {"user":"bb","uuid":"bb", "port":2}
            ]
        }
    }

    curl http://127.0.0.1:61111/webapadmin/access_rule/aa/aa -X PUT -d '{"config":{"port":5,"server":"1.1.1.1"}}'

    修改后接入规则如下：
    {
        "twaf_access_rule": {
            "rules":[
                {"user":"aa","uuid":"aa", "port":5,"server":"1.1.1.1"},
                {"user":"bb","uuid":"bb", "port":2}
            ]
        }
    }

    接入规则修改成功
```

[Back to TOC](#table-of-contents)


<a id="access_rule_delete" name="access_rule_delete">DELETE</a>
---

* API: DELETE /webapadmin/access_rule/{user}/{uuid}

    功能：删除接入规则

    注：user 为用户 ID, uuid 为接入规则 ID

```
    例：当前接入规则如下：
    {
        "twaf_access_rule": {
            "rules":[
                {"user":"aa","uuid":"aaaa"},
                {"user":"aa","uuid":"aa"},
                {"user":"aa","uuid":"aaa"},
                {"user":"bb","uuid":"bb"},
                {"user":"bb","uuid":"bbb"}
            ]
        }
    }

    curl http://127.0.0.1:61111/webapadmin/access_rule/bb/bbb -X DELETE

    接入规则变为：
    {
        "twaf_access_rule": {
            "rules":[
                {"user":"aa","uuid":"aaaa"},
                {"user":"aa","uuid":"aa"},
                {"user":"aa","uuid":"aaa"},
                {"user":"bb","uuid":"bb"}
            ]
        }
    }

    接入规则删除成功
```

[Back to TOC](#table-of-contents)

policy
======

* [GET](#policy_get)
* [POST](#policy_post)
* [PUT](#policy_put)
* [DELETE](#policy_delete)

<a id="policy_get" name="policy_get">GET</a>
---

* API: GET /webapadmin/policy/{policy_uuid}

    功能: 按策略ID查询策略信息

    注：policy_uuid 为策略 ID

```
    查询 ID 为 "haha" 的策略配置
    curl http://127.0.0.1:61111/webapadmin/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":true}},"success":1}

    查询成功
```

[Back to TOC](#table-of-contents)

<a id="policy_post" name="policy_post">POST</a>
---

* API: POST /webapadmin/policy/{policy_uuid}

    功能: 添加一份完整的策略配置

    注：policy_uuid 为策略 ID，要求全局唯一，且不能与已有策略 ID 重复

```
    例：添加一份ID为 "haha" 的策略
    curl http://127.0.0.1:61111/webapadmin/policy/haha -X POST -d '{"config":{"twaf_global":{"simulation":false}}}'

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}
    
    ------------------------------------
    
    查询ID为 "haha" 的策略配置
    curl http://127.0.0.1:61111/webapadmin/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}

    添加策略成功
```

[Back to TOC](#table-of-contents)

<a id="policy_put" name="policy_put">PUT</a>
---

* API: PUT /webapadmin/policy/{policy_uuid}

    功能: 覆盖已存在的策略

    注：policy_uuid 为已存在的策略 ID

```
    例：
    查询 ID 为 "haha" 的策略配置
    curl http://127.0.0.1:61111/webapadmin/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}

    ------------------------------------

    修改 ID 为 "haha" 的策略中 simulation 值为 true (重新下发新的策略配置，覆盖原有策略配置)
    curl http://127.0.0.1:61111/webapadmin/policy/haha -X PUT -d '{"config":{"twaf_global":{"simulation":true}}}'

    返回结果如下：
    {"result":{"twaf_global":{"simulation":true}},"success":1}

    ------------------------------------

    再次查询ID为 "haha" 的策略配置
    curl http://127.0.0.1:61111/webapadmin/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":true}},"success":1}

    修改策略成功
```

[Back to TOC](#table-of-contents)

<a id="policy_delete" name="policy_delete">DELETE</a>
---

* API: DELETE /webapadmin/policy/{policy_uuid}

    功能: 删除一份策略

    注：policy_uuid 为策略 ID

```
    例：
    查询 ID 为 "haha" 的策略配置
    curl http://127.0.0.1:61111/webapadmin/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}

    ------------------------------------

    删除 ID 为"haha"的策略配置
    curl http://127.0.0.1:61111/webapadmin/policy/haha -X DELETE

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}

    ------------------------------------
    再次查询 ID 为"haha"的策略配置
    curl http://127.0.0.1:61111/webapadmin/policy/haha

    返回结果如下：
    {"success":0,"reason":"No policy 'haha'"}

    删除策略成功
```

[Back to TOC](#table-of-contents)

stat
====

* [GET](#stat_get)
* [DELETE](#stat_delete)

有关统计模块信息，详见 [openwaf_reqstat](https://github.com/titansec/openwaf_reqstat) 模块

<a id="stat_get" name="stat_get">GET</a>
---

* API: GET /api/stat

    功能：获取全局（MAIN）统计信息

* API: GET /api/stat/{policy_uuid}

    功能：获取指定策略的统计信息

    如：获取 "twaf_policy_conf" 策略的统计信息
```
        GET 127.0.0.1:61111/api/stat/twaf_policy_conf
```

* API: GET /api/stat/policy_all

    功能：获取所有策略（包含GLOBAL）的统计信息
    
PS：  

    统计模块内置 GLOBAL 策略信息，它包含所有策略统计信息的总和  
    全局(MAIN)统计信息包含 nginx_version, loadsec, resetsec, connection 等信息  
    全局(MAIN)统计信息还包含 GLOBAL 策略  
    策略统计信息包含 access, safe, upstream 三部分信息  
        
[Back to TOC](#table-of-contents)
    
<a id="stat_delete" name="stat_delete">DELETE</a>
---
    
* API：DELETE /api/stat

    功能: 删除所有统计信息
    
[Back to TOC](#table-of-contents)

version
=======

* [GET](#version_get)

<a id="version_get" name="version_get">GET</a>
---

* API: GET /webapadmin/version

    功能: 查询 TWAF 版本

* API: GET /webapadmin/version/{module_name}

    功能: 查询指定功能模块的版本

[Back to TOC](#table-of-contents)

shm
===

* [GET](#shm_get)

<a id="shm_get" name="shm_get">GET</a>
---

* API: GET /webapadmin/shm/{shm_name}

    功能: 打印指定 shared_dict 内容

    注: shm_name 表示 shared_dict 的名称

[Back to TOC](#table-of-contents)

timer_count
===========

* [GET](#timer_count_get)

<a id="timer_count_get" name="timer_count_get">GET</a>
---

* API: GET /webapadmin/timer_count

    功能: 打印当前运行和待运行的定时器个数

```
    curl http://127.0.0.1:61111/webapadmin/timer_count

    {"result":{"pending_count":2,"running_count":0},"success":1}
        
    注:
        pending_count 表示待运行的定时器个数
        running_count 表示正运行的定时器个数
        pending_count 上限为 lua_max_pending_timers (默认1024)
        running_count 上限为 lua_max_running_timers (默认256)
```

[Back to TOC](#table-of-contents)

engine_info
===========

* [GET](#engine_info_get)

<a id="engine_info_get" name="engine_info_get">GET</a>
---

* API: GET /webapadmin/engine_info

    功能: 打印引擎部分信息

```
    curl http://127.0.0.1:61111/webapadmin/engine_info

    {
        "result": {
            "worker_exiting": false,
            "config_debug": false,
            "config_subsystem": "http",
            "config_nginx_version": 10011002,
            "worker_count": 1,
            "config_prefix": "/usr/local/openresty/nginx/",
            "timer_pending_count": 0,
            "timer_running_count": 0,
            "config_ngx_lua_version": 10006,
            "config_nginx_configure": " --prefix=/usr/local/openresty/nginx --with-cc-opt=-O2 --add-module=../ngx_devel_kit-0.3.0 --add-module=../echo-nginx-module-0.60 --add-module=../ngx-http-waf-variables-module-0.01 --add-module=../ngx-http-upstream-session-sticky-module-0.01 --add-module=../ngx-http-upstream-tunnel-module-0.01 --add-module=../ngx-http-upstream-max-conns-zone-module-0.01 --add-module=../lua-upstream-cache-nginx-module-0.1.1 --add-module=../lua-gzip-nginx-module-0.1 --add-module=../ngx_cache_purge-2.3 --add-module=../xss-nginx-module-0.05 --add-module=../ngx_coolkit-0.2rc3 --add-module=../set-misc-nginx-module-0.31 --add-module=../form-input-nginx-module-0.12 --add-module=../encrypted-session-nginx-module-0.06 --add-module=../srcache-nginx-module-0.31 --add-module=../ngx_lua-0.10.6 --add-module=../ngx_lua_upstream-0.06 --add-module=../headers-more-nginx-module-0.31 --add-module=../array-var-nginx-module-0.05 --add-module=../memc-nginx-module-0.17 --add-module=../redis2-nginx-module-0.13 --add-module=../redis-nginx-module-0.3.7 --add-module=../rds-json-nginx-module-0.14 --add-module=../rds-csv-nginx-module-0.07 --with-ld-opt=-Wl,-rpath,/usr/local/openresty/luajit/lib --with-pcre-jit --with-ipv6 --with-twaf-tproxy --with-twaf-server-addr-var --with-twaf-original-dst --without-http_autoindex_module --without-http_empty_gif_module --without-http_ssi_module --without-http_userid_module --without-http_memcached_module --without-http_charset_module --without-http_split_clients_module --with-http_stub_status_module --with-http_ssl_module --with-http_realip_module --with-http_sub_module --with-twaf-proxy-cache-ct --with-twaf-gzip-enable-var --with-pcre=/soft/pcre-8.38 --with-zlib=/soft/zlib-1.2.8 --with-twaf-keepalive --with-twaf-tunnel --add-module=/soft/mod_security/nginx/modsecurity --add-dynamic-module=/soft/webapng-4.1.9.15.1/bundle/ngx-http-twaf-header-sent-filter-module-0.01 --with-http_v2_module",
            "worker_pid": 1032
        },
        "success": 1
    }
```

[Back to TOC](#table-of-contents)
Name
====

openwaf_api 是 [openwaf](https://github.com/titansec/openwaf) 的子模块，用于控制读取/修改内存中的部分信息

TODO
====

* 支持删除指定策略的统计信息

Table of Contents
=================

* [access_rule](#access_rule)
* [rules](#rules)
* [system_rules_exclude](#system_rules_exclude)
* [user_defined_rules](#user_defined_rules)
* [policy](#policy)
* [dynamic_config](#dynamic_config)
* [dynamic_config_back](#dynamic_config_back)
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

* API: GET /api/access_rule/{user}/{uuid}

    功能：查询某用户下某条接入规则

* API: GET /api/access_rule

    功能：查询所有用户下所有接入规则

* API: GET /api/access_rule/{user}

    功能：查询某用户下所有接入规则

    注：user 为用户 ID, uuid 为接入规则 ID

[Back to TOC](#table-of-contents)

<a id="access_rule_post" name="access_rule_post">POST</a>
---

* API: POST /api/access_rule/{user}/{pos}

    功能：按索引添加接入规则。若 pos 为空时，表示添加至 user 接入规则最后一条

    注：user 为用户 ID，pos 为位置索引

* API: POST /api/access_rule/{user}/uuid/{uuid}

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
    curl http://127.0.0.1:61111/api/access_rule/aa/2 -X POST -d '{"config":{"user":"aa","uuid":"aaa"}}'

    在用户 aa 中，添加一条接入规则，且排序为 1
    curl http://127.0.0.1:61111/api/access_rule/aa/1 -X POST -d '{"config":{"user":"aa","uuid":"aaaa"}}'

    在用户 bb 中，添加一条接入规则，且排序为末尾
    curl http://127.0.0.1:61111/api/access_rule/bb -X POST -d '{"config":{"user":"bb","uuid":"bbb"}}'

    在用户 bb 中，添加一条接入规则，且添加至 uuid 为 bbb 的下一条
    curl http://127.0.0.1:61111/api/access_rule/bb/uuid/bbb -X POST -d '{"config":{"user":"bb","uuid":"bbbb"}}'

    在用户 bb 中，添加一条接入规则，且添加至第一条
    curl http://127.0.0.1:61111/api/access_rule/bb/uuid -X POST -d '{"config":{"user":"bb","uuid":"b"}}'

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

* API: PUT /api/access_rule/{user}/{uuid}

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

    curl http://127.0.0.1:61111/api/access_rule/aa/aa -X PUT -d '{"config":{"port":5,"server":"1.1.1.1"}}'

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

* API: DELETE /api/access_rule/{user}/{uuid}

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

    curl http://127.0.0.1:61111/api/access_rule/bb/bbb -X DELETE

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

rules
=====

系统特征规则 API

不推荐使用此 API，因为调用此 API 产生的配置变动，并不会被 'dynamic_config' API 备份

* [GET](#rules_get)
* [POST](#rules_post)
* [PUT](#rules_put)
* [DELETE](#rules_delete)

<a id="rules_get" name="rules_get">GET</a>
---

* API: GET /api/rules/{rule_id}

    功能: 查询系统特征规则

    注：rule_id 为全局唯一的规则 ID

* API: GET /api/rules

    功能:查看所有系统特征规则

[Back to TOC](#table-of-contents)

<a id="rules_post" name="rules_post">POST</a>
---

* API: POST /api/rules

    功能: 动态添加系统特征规则(支持一次 POST 多条特征规则)

* API: POST /api/rules/checking

    功能: 检测POST的规则格式是否正确(仅仅检测，不会添加到系统规则库中)

[Back to TOC](#table-of-contents)

<a id="rules_put" name="rules_put">PUT</a>
---

* API: PUT /api/rules

    功能: 更新已存在的系统规特征则

[Back to TOC](#table-of-contents)

<a id="rules_delete" name="rules_delete">DELETE</a>
---

* API: DELETE /api/rules/{rule_id}

    功能: 删除系统特征规则

    注: rule_id为全局唯一的规则ID

    此API暂不支持
    
[Back to TOC](#table-of-contents)

system_rules_exclude
====================

系统特征规则排除 API

```
        "system_rules_id": {                                                -- 系统特征规则排除
            "111112": [{"USERID":"123"}],                                   -- 匹配中数组中信息则对应规则失效，数组中 key 为变量名称，值支持正则
            "111113": [{"POLICYID":"xxxxx"}],                               -- 在 "xxxxx" 策略中，"111113" 规则失效
            "111114": [{REMOTE_HOST":"a.com", "URI":"^/ab"}],               -- 当域名匹配 ”a.com" 且 uri 匹配 "^/ab" 时，"111114" 规则失效
            "111115": [],                                                   -- 特征未被排除
            "111116": [{}]                                                  -- 特征被无条件排除
        }
```

* [GET](#system_rules_exclude_get)
* [POST](#system_rules_exclude_post)
* [DELETE](#system_rules_exclude_delete)

<a id="system_rules_exclude_get" name="system_rules_exclude_get">GET</a>
---

* API: GET /api/system_rules_exclude

    功能：查看全部系统特征规则排除配置

* API: GET /api/system_rules_exclude/{rule_id}

    功能：查看指定 rule id 的配置

[Back to TOC](#table-of-contents)

<a id="system_rules_exclude_post" name="system_rules_exclude_post">POST</a>
---

* API: POST /api/system_rules_exclude

    功能: 覆盖系统特征规则排除配置

[Back to TOC](#table-of-contents)

<a id="system_rules_exclude_delete" name="system_rules_exclude_delete">DELETE</a>
---

* API: DELETE /api/system_rules_exclude

    功能：删除全部系统特征规则排除配置

* API: DELETE /api/system_rules_exclude/{rule_id}

    功能：删除指定 rule id 的配置

[Back to TOC](#table-of-contents)

user_defined_rules
==================

* [GET](#user_defined_rules_get)
* [POST](#user_defined_rules_post)
* [PUT](#user_defined_rules_put)
* [DELETE](#user_defined_rules_delete)

<a id="user_defined_rules_get" name="user_defined_rules_get">GET</a>
---

* API: GET /api/user_defined_rules/{policy_uuid}/{rule_id}

    功能：查询某策略下相关特征规则信息
    
    注：policy_uuid为策略ID,rule_id为特征规则ID

```
例，查询twaf_policy_conf策略下ID为910002的规则信息
curl http://127.0.1.1:61111/api/user_defined_rules/twaf_policy_conf/910002

返回结果如下：
{"result":{"charactor_version":"001","weight":0,"charactor_name":"ZXhjZXB0aW9uLnJlcWJvZHlfZXJyb3I=","id":"910002","category":"5byC5bi46K+35rGC","opts":{"sanitise_arg":["password","passwd"],"nolog":false},"meta":403,"release_version":"858","match":[{"vars":[{"var":"REQBODY_ERROR"}],"operator":"equal","op_negated":true,"pattern":0}],"action":"deny","severity":"low","phase":"access"},"success":1}
```

[Back to TOC](#table-of-contents)

<a id="user_defined_rules_post" name="user_defined_rules_post">POST</a>
---

* API: POST /api/user_defined_rules/{policy_uuid}

    功能：添加自定义特征规则

    注：其中 policy_uuid 为策略id 添加的自定义特征规则 ID 要求全局唯一 支持一次添加多条规则
    
```
例，在 twaf_policy_conf 策略下添加一条 ID 为 910002 的特征规则：
curl http://127.0.1.1:61111/api/user_defined_rules/twaf_policy_conf -X POST -d '{"config":{"charactor_version":"001","weight":0,"charactor_name":"ZXhjZXB0aW9uLnJlcWJvZHlfZXJyb3I=","id":"910002","category":"5byC5bi46K+35rGC","opts":{"sanitise_arg":["password","passwd"],"nolog":false},"meta":403,"release_version": "858","match":[{"vars":[{"var": "REQBODY_ERROR"}],"operator": "equal","op_negated": true,"pattern": 0}],"action":"deny","severity":"low","phase":"access"}}'

返回结果如下：
{"result":[{"charactor_version":"001","weight":0,"charactor_name":"ZXhjZXB0aW9uLnJlcWJvZHlfZXJyb3I=","id":"910002","category":"5byC5bi46K+35rGC","opts":{"sanitise_arg":["password","passwd"],"nolog":false},"meta":403,"release_version":"858","match":[{"vars":[{"var":"REQBODY_ERROR"}],"operator":"equal","op_negated":true,"pattern":0}],"action":"deny","severity":"low","phase":"access"}],"success":1}
```

[Back to TOC](#table-of-contents)

<a id="user_defined_rules_put" name="user_defined_rules_put">PUT</a>
---

* API: PUT /api/user_defined_rules/{policy_uuid}/{rule_id}

    功能：更新某策略下已存在的用户自定义规则

    注：policy_uuid 为策略 ID, rule_id 为特征规则 ID

```
例，查询 twaf_policy_conf 策略下 ID 为 910002 的规则信息
curl http://127.0.1.1:61111/api/user_defined_rules/twaf_policy_conf/910002

返回结果如下：
{"result":{"charactor_version":"001","weight":0,"charactor_name":"ZXhjZXB0aW9uLnJlcWJvZHlfZXJyb3I=","id":"910002","category":"5byC5bi46K+35rGC","opts":{"sanitise_arg":["password","passwd"],"nolog":false},"meta":403,"release_version":"858","match":[{"vars":[{"var":"REQBODY_ERROR"}],"operator":"equal","op_negated":true,"pattern":0}],"action":"deny","severity":"low","phase":"access"},"success":1}

---------------------------------------------
禁用 twaf_policy_conf 策略下 ID 为 910002 规则，即规则中添加 "disable":1

curl http://127.0.1.1:61111/api/user_defined_rules/twaf_policy_conf/910002 -X PUT -d '{"config":{"disable":1,"charactor_version":"001","weight":0,"charactor_name":"ZXhjZXB0aW9uLnJlcWJvZHlfZXJyb3I=","id":"910002","category":"5byC5bi46K+35rGC","opts":{"sanitise_arg":["password","passwd"],"nolog":false},"meta":403,"release_version": "858","match":[{"vars":[{"var": "REQBODY_ERROR"}],"operator": "equal","op_negated": true,"pattern": 0}],"action":"deny","severity":"low","phase":"access"}}'

返回结果如下：
{"result":{"disable":1,"weight":0,"charactor_name":"ZXhjZXB0aW9uLnJlcWJvZHlfZXJyb3I=","id":"910002","category":"5byC5bi46K+35rGC","opts":{"sanitise_arg":["password","passwd"],"nolog":false},"meta":403,"charactor_version":"001","release_version":"858","match":[{"vars":[{"var":"REQBODY_ERROR"}],"operator":"equal","op_negated":true,"pattern":0}],"severity":"low","action":"deny","phase":"access"},"success":1}

-------------------------------------------------
再次查询 twaf_policy_conf 策略下 ID 为 910002 的规则信息
curl http://127.0.1.1:61111/api/user_defined_rules/twaf_policy_conf/910002

返回结果如下：
{"result":{"disable":1,"weight":0,"charactor_name":"ZXhjZXB0aW9uLnJlcWJvZHlfZXJyb3I=","id":"910002","category":"5byC5bi46K+35rGC","opts":{"sanitise_arg":["password","passwd"],"nolog":false},"meta":403,"charactor_version":"001","release_version":"858","match":[{"vars":[{"var":"REQBODY_ERROR"}],"operator":"equal","op_negated":true,"pattern":0}],"severity":"low","action":"deny","phase":"access"},"success":1}

禁用操作成功
```

[Back to TOC](#table-of-contents)

<a id="user_defined_rules_delete" name="user_defined_rules_delete">DELETE</a>
---

* API: DELETE /api/user_defined_rules/{policy_uuid}/{rule_id}

    功能：删除某策略下自定义特征

    注：policy_uuid 为策略 ID, rule_id 为特征规则 ID

```
例，查询 twaf_policy_conf 策略下 ID 为 910002 的特征规则信息
curl http://127.0.1.1:61111/api/user_defined_rules/twaf_policy_conf/910002

返回结果如下：
{"result":{"charactor_version":"001","weight":0,"charactor_name":"ZXhjZXB0aW9uLnJlcWJvZHlfZXJyb3I=","id":"910002","category":"5byC5bi46K+35rGC","opts":{"sanitise_arg":["password","passwd"],"nolog":false},"meta":403,"release_version":"858","match":[{"vars":[{"var":"REQBODY_ERROR"}],"operator":"equal","op_negated":true,"pattern":0}],"action":"deny","severity":"low","phase":"access"},"success":1}

-----------------------------------------
删除 twaf_policy_conf 策略下的 910002 特征规则
curl http://127.0.1.1:61111/api/user_defined_rules/twaf_policy_conf/910002 -X DELETE

返回结果如下：
{"result":{"charactor_version":"001","weight":0,"charactor_name":"ZXhjZXB0aW9uLnJlcWJvZHlfZXJyb3I=","id":"910002","category":"5byC5bi46K+35rGC","opts":{"sanitise_arg":["password","passwd"],"nolog":false},"meta":403,"release_version":"858","match":[{"vars":[{"var":"REQBODY_ERROR"}],"operator":"equal","op_negated":true,"pattern":0}],"action":"deny","severity":"low","phase":"access"},"success":1}

------------------------------------------
再次查询 twaf_policy_conf 策略下 ID 为 910002 的特征规则信息
curl http://127.0.1.1:61111/api/user_defined_rules/twaf_policy_conf/910002

返回结果如下：
{"success":0,"reason":"No such user defined rule id '910002' in policy 'twaf_policy_conf'"}

删除操作成功
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

* API: GET /api/policy/{policy_uuid}

    功能: 按策略ID查询策略信息

    注：policy_uuid 为策略 ID

```
    查询 ID 为 "haha" 的策略配置
    curl http://127.0.0.1:61111/api/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":true}},"success":1}

    查询成功
```

[Back to TOC](#table-of-contents)

<a id="policy_post" name="policy_post">POST</a>
---

* API: POST /api/policy/{policy_uuid}

    功能: 添加一份完整的策略配置

    注：policy_uuid 为策略 ID，要求全局唯一，且不能与已有策略 ID 重复

```
    例：添加一份ID为 "haha" 的策略
    curl http://127.0.0.1:61111/api/policy/haha -X POST -d '{"config":{"twaf_global":{"simulation":false}}}'

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}
    
    ------------------------------------
    
    查询ID为 "haha" 的策略配置
    curl http://127.0.0.1:61111/api/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}

    添加策略成功
```

[Back to TOC](#table-of-contents)

<a id="policy_put" name="policy_put">PUT</a>
---

* API: PUT /api/policy/{policy_uuid}

    功能: 覆盖已存在的策略

    注：policy_uuid 为已存在的策略 ID

```
    例：
    查询 ID 为 "haha" 的策略配置
    curl http://127.0.0.1:61111/api/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}

    ------------------------------------

    修改 ID 为 "haha" 的策略中 simulation 值为 true (重新下发新的策略配置，覆盖原有策略配置)
    curl http://127.0.0.1:61111/api/policy/haha -X PUT -d '{"config":{"twaf_global":{"simulation":true}}}'

    返回结果如下：
    {"result":{"twaf_global":{"simulation":true}},"success":1}

    ------------------------------------

    再次查询ID为 "haha" 的策略配置
    curl http://127.0.0.1:61111/api/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":true}},"success":1}

    修改策略成功
```

[Back to TOC](#table-of-contents)

<a id="policy_delete" name="policy_delete">DELETE</a>
---

* API: DELETE /api/policy/{policy_uuid}

    功能: 删除一份策略

    注：policy_uuid 为策略 ID

```
    例：
    查询 ID 为 "haha" 的策略配置
    curl http://127.0.0.1:61111/api/policy/haha

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}

    ------------------------------------

    删除 ID 为"haha"的策略配置
    curl http://127.0.0.1:61111/api/policy/haha -X DELETE

    返回结果如下：
    {"result":{"twaf_global":{"simulation":false}},"success":1}

    ------------------------------------
    再次查询 ID 为"haha"的策略配置
    curl http://127.0.0.1:61111/api/policy/haha

    返回结果如下：
    {"success":0,"reason":"No policy 'haha'"}

    删除策略成功
```

[Back to TOC](#table-of-contents)

dynamic_config
==============

* [GET](#dynamic_config_get)
* [POST](#dynamic_config_post)

<a id="dynamic_config_get" name="dynamic_config_get">GET</a>
---

* API: GET /api/dynamic_config

    功能：获取全量动态配置信息

    注：目前全量动态配置信息包括：接入规则和策略配置

    注：调用此 API 会在 "/opt/OpenWAF/conf/" 目录下生成 dynamic_conf_back.json 配置备份

[Back to TOC](#table-of-contents)

<a id="dynamic_config_post" name="dynamic_config_post">POST</a>
---

* API: POST /api/dynamic_config

    功能: 覆盖动态配置

[Back to TOC](#table-of-contents)

dynamic_config_back
===================

* [POST](#dynamic_config_back_post)

<a id="dynamic_config_back_post" name="dynamic_config_back_post">POST</a>
---

* API: POST /api/dynamic_config_back

    功能: 动态配置还原

    注：调用此 API，不需要请求体，它会自动加载 "/opt/OpenWAF/conf/" 目录下的 dynamic_conf_back.json 备份

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

* API: GET /api/version

    功能: 查询 TWAF 版本

* API: GET /api/version/{module_name}

    功能: 查询指定功能模块的版本

[Back to TOC](#table-of-contents)

shm
===

* [GET](#shm_get)

<a id="shm_get" name="shm_get">GET</a>
---

* API: GET /api/shm/{shm_name}

    功能: 打印指定 shared_dict 内容

    注: shm_name 表示 shared_dict 的名称

[Back to TOC](#table-of-contents)

timer_count
===========

* [GET](#timer_count_get)

<a id="timer_count_get" name="timer_count_get">GET</a>
---

* API: GET /api/timer_count

    功能: 打印当前运行和待运行的定时器个数

```
    curl http://127.0.0.1:61111/api/timer_count

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

* API: GET /api/engine_info

    功能: 打印引擎部分信息

```
    curl http://127.0.0.1:61111/api/engine_info

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
Name
====

openwaf_api是[openwaf](https://github.com/titansec/openwaf)的子模块，用于控制读取/修改内存中的部分信息

TODO
====

* 支持删除指定策略的统计信息

Api
===

* [stat](#stat)

stat
----

有关统计模块信息，详见[openwaf_reqstat](https://github.com/titansec/openwaf_reqstat)模块

* API: GET 127.0.0.1:61111/api/stat

    获取全局（MAIN）统计信息

* API: GET 127.0.0.1:61111/api/stat/{policy_uuid}

    获取指定策略的统计信息

    如：获取"twaf_policy_conf"策略的统计信息
```
        GET 127.0.0.1:61111/api/stat/twaf_policy_conf
```

* API: GET 127.0.0.1:61111/api/stat/policy_all

    获取所有策略（包含GLOBAL）的统计信息

* API：DELETE 127.0.0.1:61111/api/stat

    删除所有统计信息


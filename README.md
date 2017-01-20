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

* API: GET 127.0.0.1:61111/api/stat

    获取所有策略的统计信息

* API: GET 127.0.0.1:61111/api/stat/{policy_uuid}

    获取指定策略的统计信息

    如：获取"twaf_policy_conf"策略的统计信息
```
        GET 127.0.0.1:61111/api/stat/twaf_policy_conf
```

* API：DELETE 127.0.0.1:61111/api/stat

    删除所有统计信息


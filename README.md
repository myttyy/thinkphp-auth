### Thinkphp Auth权限认证类

2019年5月20日 17:10:54

- Thinkphp6.0请使用最新版2.*版

```
 "require": {
    "php": ">=7.1",
    "topthink/framework": ">=thinkphp5.1.*" //  或者 "topthink/framework": ">=6.0.*-dev",
 },
```

### 功能特性：
1.是对规则进行认证，不是对节点进行认证。用户可以把节点当作规则名称实现对节点进行认证。<br>

```php
$auth=new Auth();
$auth->check('规则名称','用户id')
```

2.可以同时对多条规则进行认证，并设置多条规则的关系（or或者and）

```php
$auth=new Auth();// Auth::instance
$auth->check('规则1,规则2','用户id','and')
```

> 第三个参数可选，枚举值： or 、and
> 第三个参数为and时表示，用户需要同时具有规则1和规则2的权限; 当第三个参数为or时，表示用户值需要具备其中一个条件即可。默认为or

3.一个用户可以属于多个用户组(think_auth_group_access表 定义了用户所属用户组)。我们需要设置每个用户组拥有哪些规则(think_auth_group 定义了用户组权限)

4.支持规则表达式。
在think_auth_rule 表中定义一条规则时，如果type为1， condition字段就可以定义规则表达式。 如定义{score}>5 and {score}<100 表示用户的分数在5-100之间时这条规则才会通过。


### 数据库

```sql
-- ----------------------------
-- think_auth_rule，规则表，
-- id:主键，name：规则唯一标识, title：规则中文名称 status 状态：为1正常，为0禁用，condition：规则表达式，为空表示存在就验证，不为空表示按照条件验证
-- ----------------------------
DROP TABLE IF EXISTS `think_auth_rule`;
CREATE TABLE `think_auth_rule` (
`id` mediumint(8) unsigned NOT NULL AUTO_INCREMENT,
`name` char(80) NOT NULL DEFAULT '',
`title` char(20) NOT NULL DEFAULT '',
`type` tinyint(1) NOT NULL DEFAULT '1',
`status` tinyint(1) NOT NULL DEFAULT '1',
`condition` char(100) NOT NULL DEFAULT '', # 规则附件条件,满足附加条件的规则,才认为是有效的规则
PRIMARY KEY (`id`),
UNIQUE KEY `name` (`name`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
-- ----------------------------
-- think_auth_group 用户组表，
-- id：主键， title:用户组中文名称， rules：用户组拥有的规则id， 多个规则","隔开，status 状态：为1正常，为0禁用
-- ----------------------------
DROP TABLE IF EXISTS `think_auth_group`;
CREATE TABLE `think_auth_group` (
`id` mediumint(8) unsigned NOT NULL AUTO_INCREMENT,
`title` char(100) NOT NULL DEFAULT '',
`status` tinyint(1) NOT NULL DEFAULT '1',
`rules` char(80) NOT NULL DEFAULT '',
PRIMARY KEY (`id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
-- ----------------------------
-- think_auth_group_access 用户组明细表
-- uid:用户id，group_id：用户组id
-- ----------------------------
DROP TABLE IF EXISTS `think_auth_group_access`;
CREATE TABLE `think_auth_group_access` (
`uid` mediumint(8) unsigned NOT NULL,
`group_id` mediumint(8) unsigned NOT NULL,
UNIQUE KEY `uid_group_id` (`uid`,`group_id`),
KEY `uid` (`uid`),
KEY `group_id` (`group_id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
```

### composer安装

```
composer require myttyy/thinkphp-auth
```
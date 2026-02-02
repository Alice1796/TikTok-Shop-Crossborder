# Backend API 深度扫描列表

> 方法名、输入参数、操作的数据库表。表名前缀为 `mod_`（以实际 database.prefix 为准）。

---

## 一、Index 控制器（登录注册与首页）

| 方法名 | HTTP | 输入参数 | 操作的数据库表 |
|--------|------|----------|----------------|
| **login** | POST/ANY | `username`(必填), `pass`(必填), `lang` | **mod_user**（查询、更新 login_time/login_ip）, **Cache**（写入 token） |
| **doRegister** | POST | `username`(必填), `pass`(必填), `deal_pass`(必填), `invite_code`(必填), `langs` | **mod_user**（查询邀请人、查重、更新 sub_num、插入新用户）, **mod_message**（插入注册欢迎消息） |
| **logout** | POST | 无（需 Header authorization） | **Cache**（删除 token） |
| **getArticle** | GET | `lang` | 无表（读配置 Configure 缓存） |
| **goodsList** | POST/ANY | `page` | **mod_goods**（分页查询 see=1） |
| **noticeList** | POST | `page` | **mod_announcement**（分页查询） |
| **getIndex** | POST | 无（可选 Header 带用户） | **mod_message**（未读数）, **mod_configure**（读 id=6）, **mod_level**（VIP 列表，登录时） |
| **getCustomer** | POST | 无 | 无表（读配置 system_link） |
| **getVipList** | POST | 无 | **mod_level**（查询全部） |
| **buyVip** | POST | `level_id`(必填) | **mod_user**（查用户、更新 balance/level_id）, **mod_level**（查价格）, **mod_wallet_log**（插入扣款与奖励记录） |
| **getMesList** | POST | `page`, `web_time` | **mod_message**（按 uid 分页） |
| **setMesRead** | POST | `mes_id`(必填) | **mod_message**（更新 status=1） |
| **getAppList** | POST | 无 | **mod_app_roll**（列表） |
| **getAppInfo** | POST | `id`(必填), `web_time` | **mod_app_roll**（按 id 查询） |
| **getAgreement** | POST | 无 | **mod_configure**（id=5 协议内容） |

---

## 二、Account 控制器（用户与资金）

| 方法名 | HTTP | 输入参数 | 操作的数据库表 |
|--------|------|----------|----------------|
| **getUserInfo** | POST | 无（需登录） | **mod_user**, **mod_level**, **mod_wallet_log**, **mod_order**, **mod_withdraw**（只读汇总） |
| **sign** | POST | 无（需登录） | **mod_user**, **mod_level**（只读）, **mod_sign**（插入签到） |
| **changePass** | POST | `old_pass`(必填), `new_pass`(必填), `type`(1=登录密码 2=交易密码) | **mod_user**（更新 password 或 deal_pass） |
| **getCard** | POST | 无（需登录） | **mod_bank**（按 uid 查）, 配置 system_bank |
| **setCard** | POST | `name`, `card_no`, `account_name`, `account_open_name`, `mobile`, `wise`, `revolut`, `usdt`, `type` | **mod_bank**（插入或更新）, **mod_user**（更新 bind_card）, **mod_bank_log**（插入变更记录） |
| **setWithData** | POST | `price`(必填), `pass`(必填), `type`, `langs`, `web_time`(必填) | **mod_user**, **mod_level**（校验）, **mod_order**（待支付汇总）, **mod_bank**（查提款信息）, **mod_withdraw**（插入）, **mod_user**（扣 balance、增 freeze_balance） |
| **getWithList** | POST | `page`, `web_time` | **mod_withdraw**（按 uid 分页） |
| **invest** | POST | `price` 或 `price_num`(必填), `web_time`(必填) | **mod_user**（读 balance）, **mod_recharge**（插入充值记录） |
| **getWalletList** | POST | `page`, `web_time` | **mod_wallet_log**（按 uid 分页）, **mod_goods**（关联标题） |
| **getRechargeList** | POST | `page`, `web_time` | **mod_recharge**（按 uid 分页，status=1, beizhu 为空） |

---

## 三、Trade 控制器（订单与交易）

| 方法名 | HTTP | 输入参数 | 操作的数据库表 |
|--------|------|----------|----------------|
| **setOrder** | POST/ANY | `lang`, `web_time`（可选） | **mod_graborders**（查/插/更新）, **mod_user**（余额校验）, **mod_user**（锁行）, **mod_hotsales**（查/更新）, **mod_goods**（随机商品）, **mod_order**（插入）, **mod_user**（更新 order_total 等）, **mod_wallet_log**（在 Order 模型内） |
| **commitOrder** | POST | `order_id`(必填) | **mod_order**（锁行、更新、分佣逻辑）, **mod_user**（扣款/加款/冻结）, **mod_wallet_log**（插入）, **mod_schedule**（连单逻辑） |
| **getOrderList** | POST | `page`, `web_time`, `status`, `lang` | **mod_order** + **mod_goods**（联表分页） |
| **getOrderInfo** | POST | `order_id`(必填), `web_time`, `lang` | **mod_order** + **mod_goods**（联表单条） |

---

## 四、Jiang 控制器（抽奖）

| 方法名 | HTTP | 输入参数 | 操作的数据库表 |
|--------|------|----------|----------------|
| **getList** | GET/ANY | 无 | **mod_goods2**（奖品列表，读） |
| **kaiJiang** | POST/ANY | 无（需登录） | **mod_user**（读/更新 jiang、jiangk）, **mod_goods2**（读奖品、权重）, **mod_goods3**（插入中奖记录） |

---

## 数据库表汇总

| 表名（推测） | 说明 |
|-------------|------|
| mod_user | 用户账号、余额、等级、邀请关系等 |
| mod_message | 站内消息 |
| mod_configure | 系统配置（多条 JSON content） |
| mod_level | VIP 等级与价格 |
| mod_goods | 商品/任务 |
| mod_announcement | 公告 |
| mod_wallet_log | 钱包流水 |
| mod_app_roll | App 滚动内容 |
| mod_bank | 用户银行卡/收款信息 |
| mod_bank_log | 银行卡变更日志 |
| mod_withdraw | 提现申请 |
| mod_recharge | 充值记录 |
| mod_sign | 签到记录 |
| mod_order | 订单 |
| mod_graborders | 抢单/抓单状态 |
| mod_hotsales | 热销/连单活动 |
| mod_schedule | 连单规则 |
| mod_goods2 | 抽奖奖品配置 |
| mod_goods3 | 抽奖中奖记录 |

---

*说明：路由定义在 `app/api/route/api.php`，需登录的接口通过中间件 CheckApi 从 Header 取 authorization 并解析用户信息。*

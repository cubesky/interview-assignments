由于题目要求中要求放在 JVM 内存上，那么为了简化模型，使用 BiMap 双向映射糊列表值。同时也可以考虑使用 Sqlite In-Memory 数据库进行存储。
考虑到这个是一个短网址服务，并只有8位长度，同时也是一个单点系统，所以采用序列发号的方式。在 UrlGenerateService 上使用同步关键字，保证发号顺序正确并不会出现重复的情况。
在短网址服务中，缩短时的边界条件用户未输入内容或者发号服务已到达上线。所以在这两种情况下，返回非 200 返回值表示不能缩短链接。同时使用不同的返回值进行处理，让前端可以分辨出原因并提供不同的说明。
在使用短网址跳转时可能发生用户未输入内容或者输入的短网址不存在。在未输入内容和短网址不存在时，返回 404 返回值，如果处于跳转接口上，则重定向到 404 页面。

我观察到 Java 题目发生过变化，所以我也想说一下在之前的题目情况下的考虑。
之前可以考虑使用 Snowflake 算法的分布式一致性发号算法，让整个系统可以快速拓展，避免单服务器瓶颈。因为一般短连接访问要远远多于缩短，即读大于写，所以可以考虑通过 Redis 或者 Memcached 缓存热门链接，提升访问速度。
同时使用数据库时，可以使用主从服务器切换等方法，提升数据库的可用性。
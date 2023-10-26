# README

Sequence 是一个雪花 ID 生成包，创建 `cc.ddrpa.repack.sequence.Sequence` 实例后，可通过 `cc.ddrpa.repack.sequence.Sequence::nextId` 方法获取雪花 ID。

代码从 MyBatis-Plus 的 `com.baomidou.mybatisplus.core.incrementer.DefaultIdentifierGenerator` 中提取，
原始构思来自 [yu120/sequence](https://gitee.com/yu120/sequence)（MIT 许可），本项目仅做打包操作并发布到中央仓库。

本项目制品通过 sonatype.org 同步到 Maven 中央仓库，
可访问 [Sonatype Maven Central Repository](https://central.sonatype.com/artifact/cc.ddrpa.repack/sequence) 或
[MvnRepository](https://mvnrepository.com/artifact/cc.ddrpa.repack/sequence) 获取。
由于本人并非代码作者，不会对版本进行更新。

```xml
<dependency>
    <groupId>cc.ddrpa.repack</groupId>
    <artifactId>sequence</artifactId>
    <version>1.0.0</version>
</dependency>
```

### 在 JPA & Hibernate 以及其他场合中使用

实现 `org.hibernate.id.IdentifierGenerator` 接口

``` java
public class SnowflakeIdGenerator implements IdentifierGenerator {
    private final Sequence sequence;

    public SnowflakeIdGenerator() {
        this.sequence = new Sequence();
    }

    public SnowflakeIdGenerator(long workerId, long dataCenterId) {
        this.sequence = new Sequence(workerId, dataCenterId);
    }

    public SnowflakeIdGenerator(Sequence sequence) {
        this.sequence = sequence;
    }

    @Override
    public Serializable generate(SharedSessionContractImplementor sharedSessionContractImplementor, Object o) throws HibernateException {
        return sequence.nextId();
    }
}
```

在 Entity 的 `@Id` 中指明对应的ID生成策略

``` java
@Entity
public class Ticket implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "snowflake")
    @GenericGenerator(name = "snowflake", strategy = "cc.ddrpa.playground.SnowflakeIdGenerator")
    private Long id;
}
```

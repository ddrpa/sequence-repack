# README

Sequence 是一个雪花 ID 生成包，使用 `new Sequence()` 创建一个实例后，可以不断通过 `Sequence.nextId()` 获取 ID。

代码逻辑从 MyBatis-Plus 的 `com.baomidou.mybatisplus.core.incrementer.DefaultIdentifierGenerator` 中提取，原始代码来自 [yu120/sequence](https://gitee.com/yu120/sequence)，本项目仅做打包和发布到中央仓库操作。

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

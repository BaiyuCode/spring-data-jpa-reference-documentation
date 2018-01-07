#
4.1.2. ����ע�͵�����

Spring Data JPA�洢��֧�ֲ�������ͨ��XML�����ռ伤�������ͨ��JavaConfigʹ��ע�͡�

##### _��50.ʹ��JavaConfig��Spring Data JPA�洢��_

```java
@Configuration
@EnableJpaRepositories
@EnableTransactionManagement
class ApplicationConfig {

@Bean
public DataSource dataSource() {

EmbeddedDatabaseBuilder builder = new EmbeddedDatabaseBuilder();
return builder.setType(EmbeddedDatabaseType.HSQL).build();
}

@Bean
public LocalContainerEntityManagerFactoryBean entityManagerFactory() {

HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
vendorAdapter.setGenerateDdl(true);

LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
factory.setJpaVendorAdapter(vendorAdapter);
factory.setPackagesToScan("com.acme.domain");
factory.setDataSource(dataSource());
return factory;
}

@Bean
public PlatformTransactionManager transactionManager() {

JpaTransactionManager txManager = new JpaTransactionManager();
txManager.setEntityManagerFactory(entityManagerFactory());
return txManager;
}
}
```

> ��Ҫ����Ҫ����`LocalContainerEntityManagerFactoryBean`����`EntityManagerFactory`ֱ�ӣ���Ϊǰ�߻��μ����쳣ת�����ƣ����˼򵥵ش���`EntityManagerFactory`��

�ո���ʾ��������ʹ��`EmbeddedDatabaseBuilderspring`-jdbc ��API ����Ƕ��ʽHSQL���ݿ⡣Ȼ����������һ��`EntityManagerFactory`��ʹ��Hibernate��Ϊʾ���־û��ṩ�ߡ��������������һ��������ʩ�����`JpaTransactionManager`����������ʹ��`@EnableJpaRepositories`�����ϴ�����XML���ƿռ���ͬ�����Ե�ע��������Spring Data JPA�洢�⡣���û�����û��������������ʹ�����������ڵ��������

# 4.2. �־û�ʵ��

#### 4.2.1. ����ʵ�� {#jpa.entity-persistence.saving-entites}

����һ��ʵ�����ͨ��CrudRepository.save\(��\)-Method ��ִ�С������־û���ϲ�������ʵ��ʹ�û�����JPA EntityManager�����ʵ��û�б����棬Spring Data JPA��ͨ������entityManager.persist\(��\)��������ʵ�壬�����entityManager.merge\(��\)�����������á�

##### ʵ��״̬������ {#_entity_state_detection_strategies}

Spring Data JPA�ṩ�����²��������һ��ʵ���Ƿ����µģ�

_��3.���ڼ��ʵ����Spring Data JPA���Ƿ�������ѡ��_

| ID���Լ��\(Ĭ��\) | Ĭ������£�Spring Data JPA������ʵ��ı�ʶ�����ԡ������ʶ��������null�����ʵ�彫����Ϊ���µģ��������µġ� |
| :--- | :--- |
| ʵ��Persistable | ���һ��ʵ��ʵ��Persistable��Spring Data JPA�����µļ��ί�и�isNew\(��\)ʵ��ķ������й���ϸ��Ϣ�������[JavaDoc��](https://docs.spring.io/spring-data/data-commons/docs/current/api/index.html?org/springframework/data/domain/Persistable.html) |
| ʵ��EntityInformation | ������ͨ���������ಢ��Ӧ����д�÷������Զ���ʵ����`EntityInformation`ʹ�õĳ���Ȼ�������뽫�Զ����ʵ��ע��Ϊһ��Spring bean����ע�⣬��Ӧ���Ǻ����б�Ҫ�ġ��й���ϸ��Ϣ�������[JavaDoc](https://docs.spring.io/spring-data/data-jpa/docs/current/api/index.html?org/springframework/data/jpa/repository/support/JpaRepositoryFactory.html)��`SimpleJpaRepositoryJpaRepositoryFactorygetEntityInformation(��)JpaRepositoryFactory` |

### 4.3. ��ѯ���� {#jpa.query-methods}

#### 4.3.1.��ѯ��ѯ���� {#jpa.sample-app.finders.strategies}

JPAģ��֧����String��ʽ�ֶ������ѯ�����ߴӷ�������������

##### �����Ĳ�ѯ {#_declared_queries}

���ܴӷ�������ò�ѯ�Ƿǳ�����ģ����ǿ��ܻ������������������������������֧��Ҫʹ�õĹؼ��֣����߷������ƻ��ò���Ҫ���ѿ������ԣ������ͨ������Լ��ʹ��JPA������ѯ�������[ʹ��JPA NamedQueries](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.named-queries)��ȡ������Ϣ�����൱����ע����Ĳ�ѯ����`@Query`�������[ʹ��@Query](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.at-query)�˽���ϸ��Ϣ����

#### 4.3.2.��ѯ���� {#jpa.query-methods.query-creation}

ͨ����JPA�Ĳ�ѯ�������ư�[��ѯ�����е�](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods)����������������JPA��ѯ����ת��Ϊ��һ�����ʾ��:

##### _��51.�ӷ������ƴ�����ѯ_

```java
public interface UserRepository extends Repository<User, Long> {

List<User> findByEmailAddressAndLastname(String emailAddress, String lastname);
}
```

���ǽ�ʹ�ô����JPA��׼API��ѯ����������ת��Ϊ���²�ѯ��select u from User u where u.emailAddress = ?1 and u.lastname = ?2��Spring Data JPA��ִ�����Լ�鲢����[���Ա��ʽ](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.query-property-expressions)��������Ƕ�����ԡ������Ƕ�JPA֧�ֵĹؼ��ֵĸ������Լ������ùؼ��ֱ��ʵķ��뷽����

_��4.����������֧�ֵĹؼ���_

| **�ؼ���** | **ʾ��** | **JPQLƬ��** |
| :--- | :--- | :--- |
| And | findByLastnameAndFirstname | �� where x.lastname = ?1 and x.firstname = ?2 |
| Or | findByLastnameOrFirstname | �� where x.lastname = ?1 or x.firstname = ?2 |
| Is,Equals | findByFirstname��findByFirstnameIs��findByFirstnameEquals | �� where x.firstname = ?1 |
| Between | findByStartDateBetween | �� where x.startDate between ?1 and ?2 |
| LessThan | findByAgeLessThan | �� where x.age &lt; ?1 |
| LessThanEqual | findByAgeLessThanEqual | �� where x.age &lt;= ?1 |
| GreaterThan | findByAgeGreaterThan | �� where x.age &gt; ?1 |
| GreaterThanEqual | findByAgeGreaterThanEqual | �� where x.age &gt;= ?1 |
| After | findByStartDateAfter | �� where x.startDate &gt; ?1 |
| Before | findByStartDateBefore | �� where x.startDate &lt; ?1 |
| IsNull | findByAgeIsNull | �� where x.age is null |
| IsNotNull,NotNull | findByAge\(Is\)NotNull | �� where x.age not null |
| Like | findByFirstnameLike | �� where x.firstname like ?1 |
| NotLike | findByFirstnameNotLike | �� where x.firstname not like ?1 |
| StartingWith | findByFirstnameStartingWith | �� where x.firstname like ?1�������󶨸���%�� |
| EndingWith | findByFirstnameEndingWith | �� where x.firstname like ?1��������Ԥ�Ȱ�%�� |
| Containing | findByFirstnameContaining | �� where x.firstname like ?1��������%�� |
| OrderBy | findByAgeOrderByLastnameDesc | �� where x.age = ?1 order by x.lastname desc |
| Not | findByLastnameNot | �� where x.lastname &lt;&gt; ?1 |
| In | findByAgeIn\(Collection&lt;Age&gt; ages\) | �� where x.age in ?1 |
| NotIn | findByAgeNotIn\(Collection&lt;Age&gt; ages\) | �� where x.age not in ?1 |
| True | findByActiveTrue\(\) | �� where x.active = true |
| False | findByActiveFalse\(\) | �� where x.active = false |
| IgnoreCase | findByFirstnameIgnoreCase | �� where UPPER\(x.firstame\) = UPPER\(?1\) |

> `In`����`NotIn`�����Խ����κ�`Collection`�����������Լ������ɱ�����������߼��������ͬ�������﷨�汾������洢���ѯ�ؼ��֡�

#### 4.3.3.ʹ��JPA NamedQueries {#jpa.query-methods.named-queries}

> ��Щʾ��ʹ�ü򵥵�&lt;named-query /&gt;Ԫ�غ�@NamedQueryע�͡���Щ����Ԫ�صĲ�ѯ������JPA��ѯ�����ж��塣��Ȼ�������ʹ��&lt;named-native-query /&gt;��@NamedNativeQuery������ЩԪ��������ͨ��ʧȥ���ݿ�ƽ̨���������ڱ���SQL�ж����ѯ��

### XML������ѯ����

Ҫʹ��XML���ã�ֻ�轫��Ҫ��`<named-query />`Ԫ����ӵ�`orm.xml`λ��`META-INF`classpath�ļ����е�JPA�����ļ��С�������ѯ���Զ�������ͨ��ʹ��һЩ���������Լ�������õġ��йظ������飬���������

##### _��52. XML������ѯ����_

```java
<named-query name="User.findByLastname">
<query>select u from User u where u.lastname = ?1</query>
</named-query>
```

�������������ģ���ѯ��һ����������֣���������ʱ������������

##### ע������ {#_annotation_configuration}

ע�����õĺô��ǲ���Ҫ����������ļ����б༭�����ܻή��ά���ɱ�������ҪΪÿ���µĲ�ѯ�������±����������࣬�Ӷ�Ϊ�˸������ۡ�

_**��53.����ע�͵�������ѯ����**_

```java
@Entity
@NamedQuery(name = "User.findByEmailAddress",
query = "select u from User u where u.emailAddress = ?1")
public class User {

}
```



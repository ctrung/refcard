## Flexible Injection Points : from @Lazy to ObjectProvider

[Dependency Injection Revisited by Juergen Hoeller @ Spring I/O 2025](https://www.youtube.com/watch?v=AvZEoxH_wGo)

```java
// Lazy injection point
@Bean
public MyRepository myRepository(@Lazy DataSource dataSource) {
  // Defer actual method calls on DataSource proxy
  // Useful for concurrent bootstrapping, circular dependencies


// Nullable injection point
// Spring or JSpecify @Nullable all supported
// Change the default to allow null to be injected
@Bean
public MyRepository myRepository(@Nullable DataSource) {
  if (dataSource != null) ...


// Optional injection point
// Alternative to @Nullable
@Bean
public MyRepository myRepository(Optional<DataSource> dataSource) {
  if (dataSource.isPresent()) ...


// ObjectProvider with On-Demande Retrieval
@Bean
public MyRepository myRepository(ObjectProvider<DataSource> dsp) {
  // Defer retrieval of actual DataSource - no proxy
  DataSource dataSource = dsp.getObject();


// ObjectProvider with Availibility Check
@Bean
public MyRepository myRepository(ObjectProvider<DataSource> dsp) {
  DataSource dataSource = dsp.getIfAvailable();
  if (dataSource != null) ...


// ObjectProvider with Functional Style
@Bean
public MyRepository myRepository(ObjectProvider<DataSource> dsp) {
  dsp.ifAvailable(dataSource -> ...);


// Inject Point for Multiple Beans in Set
@Bean
public MyRepository myRepository(Set<DataSource> dataSources) {
  if (dataSources.size() > 1) ...


// Inject Point for Multiple Beans in List
@Bean
public MyRepository myRepository(List<DataSource> dataSources) {
  for (DataSource dataSource : dataSources) {
    // Iteration in order (Ordered/@Order)


// Inject Point for Multiple Beans in Array
@Bean
public MyRepository myRepository(DataSource[] dataSources) {
  for (DataSource dataSource : dataSources) {
    // Iteration in order (Ordered/@Order)


// Object Provider with Iteration
@Bean
public MyRepository myRepository(ObjectProvider<DataSource> dsp) {
  for (DataSource dataSource : dsp) {
    // On-demand instanciation of iterated beans


// Object Provider with Stream Access
@Bean
public MyRepository myRepository(ObjectProvider<DataSource> dsp) {
  dsp.stream().forEach(dataSource -> ...);
  // On-demand instanciation of beans in stream


// Object Provider with Ordered Stream
@Bean
public MyRepository myRepository(ObjectProvider<DataSource> dsp) {
  dsp.orderedStream().forEach(dataSource -> ...);
  // Ordering requires pre-instanciation of all matching beans


// Injection Point with Qualifier Annotation
@Bean
public MyRepository myRepository(@MyQualifier DataSource dataSource) {
  // DataSource selected for @MyQualifier match


// Injection Point with Qualifier Name
@Bean
public MyRepository myRepository(@Qualifier("myDataSource") DataSource dataSource) {
  // DataSource selected for myDataSource name match


// Injection Point with Qualified Dependency Name
@Bean
public MyRepository myRepository(DataSource myDataSource) {
  // DataSource selected for myDataSource name match if possible


// Object Provider with Qualifier Annotation
@Bean
public MyRepository myRepository(@MyQualifier ObjectProvider<DataSource> dsp) {
  DataSource dataSource = dsp.getIfAvailable();
  // DataSource selected for @MyQualifier match


// Object Provider with Qualifier Name
@Bean
public MyRepository myRepository(@Qualifier("myDataSource") ObjectProvider<DataSource> dsp) {
  DataSource dataSource = dsp.getIfAvailable();
  // DataSource selected for myDataSource name match


// Object Provider with Qualifier Dependency Name
@Bean
public MyRepository myRepository(ObjectProvider<DataSource> myDataSource) {
  DataSource dataSource = dsp.getIfAvailable();
  // DataSource selected for myDataSource name match if possible
```

ObjectProvider in Spring Framework 6.2
* A powerful retrieval handle for a given bean type
* On-demand/stream access for declarative injection points
* ObjectProvider itself not bound to bean names or annotations
* Also available from BeanFactory : getBeanProvider(beanType)

## Flexible Bean Definitions : from @Bean to BeanRegistrar
[Dependency Injection Revisited by Juergen Hoeller @ Spring I/O 2025 @ 34m40s](https://youtu.be/AvZEoxH_wGo?t=2080)

```java
// DataSource in Configuration Class
@Bean
public DataSource dataSource() {
  return new BasicDataSource(...);
}


// DataSource with Lazy Initialization
@Bean @Lazy
public DataSource dataSource() {
  return new BasicDataSource(...);
}


// DataSource as Primary
@Bean @Lazy @Primary
public DataSource dataSource() {
  return new BasicDataSource(...);
}


// DataSource with Qualifier Annotation
@Bean @Lazy @MyQualifier
public DataSource dataSource() {
  return new BasicDataSource(...);
}


// DataSource with Qualifier Name
@Bean @Lazy @Qualifier("myDataSource")
public DataSource dataSource() {
  return new BasicDataSource(...);
}


// DataSource with Qualified Bean Name
@Bean @Lazy
public DataSource myDataSource() {
  return new BasicDataSource(...);
}


// Importing a Configuration Class
@Configuration
@Import(MyDatabaseConfig.class)
public class MyRepositoryConfig {
  ...
}


// Defining the DataSource in a BeanRegistrar
public class MyDatabaseRegistrar implements BeanRegistrar {
  @Override
  public void register(BeanRegistry registry, Environment env) {
    registry.registerBean("dataSource", DataSource.class,
	  spec -> spec.lazyInit().primary().supplier(
	    context -> new BasicDataSource(...)));
  }
}


// Registering Multiple Beans in a BeanRegistrar
public class MyDatabaseRegistrar implements BeanRegistrar {
  @Override
  public void register(BeanRegistry registry, Environment env) {
    registry.registerBean("dataSource", DataSource.class,
	  spec -> spec.lazyInit().primary().supplier(
	    context -> new BasicDataSource(...)));
    if (statisticsAvailable) {
      registry.registerBean("dbStatistics", DbStatistics.class,
        spec -> spec.lazyInit().supplier(
	      context -> new DbStatistics(context.bean(DataSource.class))));
    }
  }
}


// Importing a BeanRegistrar
@Configuration
@Import(MyDatabaseRegistrar.class)
public class MyRepositoryConfig {
  ...
}
```

BeanRegistrar in Spring Framework 7.0
* Importable unit of programmatic configuration
* Structural flexibility : if conditions, for loops, etc...
* AOT-friendly, tooling fridendly, building block for DSLs
* Also via GenericApplicationContext.register(BeanRegistrar...)

Configuration Classes vs. BeanRegistrar
* BeanRegistrar is a DSL-style alternative to configuration classes
* BeanRegistrar is primarly meant for reusable common beans
* @Bean methods can declare custom qualifier annotations etc.
* Most user configuration to be written as configuration classes

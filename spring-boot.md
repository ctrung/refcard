
## Liens 

https://spring.io \
https://spring.io/blog \
https://start.spring.io

[Chaîne YT SpringDeveloper](https://www.youtube.com/user/SpringSourceDev) \
[Chaîne YT Spring I/O](https://www.youtube.com/c/SpringIOConference)

## Présentation nouvelles releases

2.0 : [Prez par Phil Webb et Madhura Bhave](https://www.youtube.com/watch?v=TasMZsZxLCA) \
2.3 : [Prez par Phil Webb](https://www.youtube.com/watch?v=WL7U-yGfUXA) \
2.4 : [Prez par Phil Webb](https://www.youtube.com/watch?v=lgyO9C9zdrg) \
2.6 : [Prez par Dan Vega](https://www.youtube.com/watch?v=4L4LEnawcO8) \
3.0-SNAPSHOT (26 oct 2022) : [Mini préz avant 1ère par Josh Long](https://www.youtube.com/watch?v=aUm5WZjh8RA)

## Tips

### Démarrage

`SpringApplication.run(DemoApplication.class, args)` est un raccourci de `new SpringApplication(DemoApplication.class).run(args)`.

La version "non raccourcie" permet de configurer en détail l'objet SpringApplication, par exemple attacher un `ApplicationStartup` (**2.4**).

```java
SpringApplication springApplication = new SpringApplication(DemoApplication.class);
springApplication.setApplicationStartup(new FlightRecorderApplicationStartup());
springApplication.run(args);
```

Ou encore d'attacher des properties programatiquement : 

```java
SpringApplication springApplication = new SpringApplication(DemoApplication.class);
Properties properties = new Properties();
properties.put("spring.batch.job.enabled", false);
application.setDefaultProperties(properties);
```

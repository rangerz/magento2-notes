# Magento 2 Notes



## Table of Contents

- [Magento 2](#Magento-2)
- [Magento Cloud](#Magento-Cloud)
- [Magento Modules](#Magento-Modules)
- [Magento Development](#Magento-Development)
- [Magento Certification](#Magento-Certification)
- [Magento Blog](#Magento-Blog)
- [Resources](#Resources)



## Magento 2



### Source Code

- [magento2](https://github.com/magento/magento2)
- [magento2-sample-data](https://github.com/magento/magento2-sample-data)
- [magento-cloud](https://github.com/magento/magento-cloud)
- Magento Cloud Suite
  - [ece-tools](https://github.com/magento/ece-tools)
  - [Magento Cloud Patches](https://github.com/magento/magento-cloud-patches)
  - [Magento Cloud Docker](https://github.com/magento/magento-cloud-docker)
  - [Magento Cloud Components](https://github.com/magento/magento-cloud-components)



### Configure Services

- Service EOL - https://github.com/magento/ece-tools/blob/develop/config/eol.yaml
- [Version support and compatibility](https://devdocs.magento.com/cloud/project/services.html#service-versions)

| SERVICE         | MAGENTO 2.4      | MAGENTO 2.3                                                  | MAGENTO 2.2                                                  |
| :-------------- | :--------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `elasticsearch` | 7.7              | **Magento 2.3.5+** — 5.2, 6.5, 6.8, 7.5, 7.7 <br />**Magento 2.3.1 - 2.3.4** — 5.2, 6.5 <br />**Magento version 2.3.0**— 5.2 | **Magento 2.2.8+** — 5.2, 6.5 <br />**Magento 2.2.0 to 2.2.7 **— 5.2 |
| `mariadb`       | 10.2, 10.3, 10.4 | **Magento 2.3.0 - 2.3.5** — 10.1 to 10.2                     | 10.1 to 10.2                                                 |
| `nginx`         |                  | 1.9                                                          | 1.9                                                          |
| `node`          |                  | 6, 8, 10, 11                                                 | 6, 8, 10, 11                                                 |
| `php`           | 7.3, 7.4         | **Magento 2.3.4+** — 7.1, 7.2, 7.3<br />**Magento 2.3.3** — 7.1, 7.2, 7.3 <br />**Magento 2.3.0 - 2.3.2** — 7.1, 7.2 | **Magento 2.2.10+** — 7.1, 7.2 <br />**Magento 2.2.5 - 2.2.9** — 7.0, 7.1 <br />**Magento 2.2.4 and earlier **— 7.0.2, 7.0.4, ~7.0.6, 7.1  <br />**Note:** Beginning with ece-tools v2002.1.0, you must use PHP version 7.1.3 or later for both Magento 2.2 and 2.3. |
| `rabbitmq`      | 3.8              | **Magento 2.3.5** — 3.8<br /> **Magento 2.3.3 - 2.3.4** — 3.7, 3.8 <br />**Magento 2.3.0 - 2.3.3** — 3.7 | 3.5                                                          |
| `redis`         | 5.x              | **Magento 2.3.1 - 2.3.5 **— 4.x, 5.x <br />**Magento 2.3.0** — 3.2, 4.0 | 3.2, 4.0, 5.0                                                |
| `varnish`       | 6.x              | **Magento 2.3.3 - 2.3.5**— 4.0, 5.0, 6.2 <br />**Magento 2.3.0 - 2.3.2**— 4.0, 5.0 | 4.0, 5.0 <br />**Note:** On Cloud projects, you must use the [Fastly service](https://devdocs.magento.com/cloud/cdn/cloud-fastly.html) for caching. Varnish is available only for local development. |





## Magento Cloud



### [ece-tools](https://devdocs.magento.com/cloud/project/ece-tools-update.html)

```bash
composer update magento/ece-tools --with-dependencies
```



### [service version](https://support.magento.com/hc/en-us/articles/360048603692-Magento-best-practice-update-services-and-components-to-latest-version)

- `.magento/services.yaml`

  ```
  mysql:
      type: mysql:10.3
      disk: 2048
  
  redis:
      type: redis:5.0
  
  elasticsearch:
      type: elasticsearch:7.7
      disk: 1024
  ```

- `.magento.app.yaml`

  ```
  runtime:
      extensions:
          - redis
  
  relationships:
      redis: "redis:redis"
  ```



### [Database](https://support.magento.com/hc/en-us/articles/360041997312)

- Improve performance
  - [Convert all MyISAM tables to InnoDb](https://support.magento.com/hc/en-us/articles/360041997312#convert)
  - [Use ElasticSearch instead of MySQL native FULLTEXT indexes](https://support.magento.com/hc/en-us/articles/360041997312#ElasticSearch)
  - [Avoid Triggers](https://support.magento.com/hc/en-us/articles/360041997312#Triggers)
  - [Switch indexer mode safely](https://support.magento.com/hc/en-us/articles/360041997312#indexer)
  - [Avoid running DDL (Data Definition Language) statements](https://support.magento.com/hc/en-us/articles/360041997312#DDL_statements)
- [Most common issues](https://support.magento.com/hc/en-us/articles/360041739651-Most-common-database-issues-in-Magento-Commerce-Cloud)
  - Long running queries
  - Primary keys are not defined
  - Duplicate indexes



### [Slave connection](https://support.magento.com/hc/en-us/articles/360049045351-Magento-Commerce-Cloud-best-practice-for-slave-connection-)

- `.magento.env.yaml`

  ```yaml
  stage:
    deploy:
      MYSQL_USE_SLAVE_CONNECTION: true
      REDIS_USE_SLAVE_CONNECTION: true
  ```



### PHP, [Opcache](https://support.magento.com/hc/en-us/articles/360044740812-Best-practice-for-OPcache-memory-size-Magento-Commerce-Cloud-Pro-plan), [Realpath](https://support.magento.com/hc/en-us/articles/360045176771-Realpath-cache-size-best-practice)

- `php.ini`

  ```ini
  opcache.memory_consumption = 2048
  realpath_cache_size = 10M
  ```



### [CSS and JS Settings](https://support.magento.com/hc/en-us/articles/360044482152-Optimize-CSS-and-JS-files-Magento-Commerce-Cloud-and-Magento-Commerce)

- **Admin** > **Stores** > **Setting** > **Configuration** > **Advanced** > **Developer** > **CSS and JavaScript Settings**

- `app/etc/config.php`

  ```
  'system' => [
    'default' => [
      'dev' => [
        'js' => [
          'merge_files' => '0',
          'minify_files' => '1',
          'move_script_to_bottom' => '1',
          'enable_js_bundling' => '0'
         ],
         'css' => [
          'minify_files' => '1',
          'merge_css_files' => '0'
         ]
  ```



### Asynchronous operation (For performance)

- [Indexer (Update On Schedule)](https://support.magento.com/hc/en-us/articles/360040227191-Indexers-Update-On-Schedule-optimizes-Magento-performance-)

  ```bash
  bin/magento indexer:show-mode [indexer]
  bin/magento indexer:set-mode {realtime|schedule} [indexer]
  ```

- [Email notification](https://support.magento.com/hc/en-us/articles/360048170772-Best-practice-Magento-order-placement-performance-)

  **Sales** > **Sales Emails** > **General Settings** > **Asynchronous Sending**

- [Orders processing](https://support.magento.com/hc/en-us/articles/360048545492-Magento-best-practice-enable-asynchronous-orders-processing)

  `bin/magento config:set dev/grid/async_indexing 1`

  **Advanced** > **Developer** > **Asynchronous indexing**



### [Disable report if not using](https://support.magento.com/hc/en-us/articles/360048862272-Magento-best-practice-disable-Reports-if-not-using)

- **General** > **Reports** > **General Options** > **Enable Reports** > *No*



### Limitation (Recommendation)

- [Sites, stores, and store views](https://support.magento.com/hc/en-us/articles/360044674792-Best-practices-number-of-sites-stores-and-store-views-Magento-Commerce-Cloud-Pro)

  - Stores: 50
  - Sites: 50
  - Store views: 50

- [EAV attribute sets: 1000](https://support.magento.com/hc/en-us/articles/360045041092-Best-practice-for-attribute-SET-in-Magento)

- [Product attributes: 500](https://support.magento.com/hc/en-us/articles/360048256612-Best-practice-Magento-product-attributes)

- [Product options: 100](https://support.magento.com/hc/en-us/articles/360048723372-Best-practices-for-Magento-product-options-)

- [Product attribute options: 100](https://support.magento.com/hc/en-us/articles/360049130851-Best-practices-for-Magento-product-attribute-options-)

- [Sale rules: 1000](https://support.magento.com/hc/en-us/articles/360045957511-Max-sales-rules-best-practice-in-Magento)

- [Products in cart: 100](https://support.magento.com/hc/en-us/articles/360046163611-Magento-frontend-performance-best-practices)

- [coupons: 250000](https://support.magento.com/hc/en-us/articles/360048171672-Maximum-number-of-coupons-in-Magento)

- [Products per page: 48](https://support.magento.com/hc/en-us/articles/360048176472-Products-per-page-limit-Magento)

- [Categories: 300](https://support.magento.com/hc/en-us/articles/360048176832-Best-practice-Magento-number-of-categories-limits)

- [Products: 10M](https://support.magento.com/hc/en-us/articles/360045066791-Product-limits-best-practice)

- [Product variations: 50](https://support.magento.com/hc/en-us/articles/360048727271-Best-practice-Magento-product-variations-)



## Magento Modules





## Magento Development



### [Frontent performance tools](https://support.magento.com/hc/en-us/articles/360046163611-Magento-frontend-performance-best-practices)

- [Google Lighthouse](https://web.dev/measure/)
- [Google PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/)



## Magento Certification



### Adobe Certified Professional

- [[AD0-E702] Magento Commerce Developer](https://spark.adobe.com/page/saM506xrYKi0p/)
  - Previously: Magento Associate Developer
  - For 1st M2 cert, dev < 2 year exp.

### Adobe Certified Expert

- [[AD0-E700] Magento Commerce Business Practitioner](https://spark.adobe.com/page/87jLctNB6KxM0/)
  - Previously: Magento Solution Specialist
  - For PMs, Sales, tech architects
- [[AD0-E701] Magento Commerce Dront-End Developer](https://spark.adobe.com/page/lJWBfPhvp5npA/)
  - Previously: Magento Frontend Developer
- [[AD0-E703] Magento Commerce Developer](https://spark.adobe.com/page/OwpmK2JcACwhl/)
  - Previously: Magento Professional Developer
- [[AD0-E705] Magento Commerce JavaScript Developer](https://spark.adobe.com/page/ZDus3If2u1rQf/)
  - Previously: Magento JavaScript Developer
- [[AD0-E706] Magento Commerce Cloud Developer](https://spark.adobe.com/page/GrkbPktpWMkkb/)
  - Previously: Magento Cloud Developer
- [[AD0-E707] Magento Commerce Order Management Developer](https://spark.adobe.com/page/K2Wjl6Na3BY0h/)
  - Previously: Magento Order Management

### Adobe Certified Master

- [[AD0-E704] Magento Commerce Architect](https://spark.adobe.com/page/PTidDHsT5JgyB/)
  - Previously: Magento Professional Developer Plus



### Courses

- [Magento Developer Documentation](https://devdocs.magento.com/)
- [Magento Certification Study Guide - BelVG](https://belvg.com/tutorial)
- [magento-notes/magento2-exam-notes](https://github.com/magento-notes/magento2-exam-notes)
- [magento-notes/magento2-cloud-developer-notes](https://github.com/magento-notes/magento2-cloud-developer-notes)
- [Certification Resources - SwiftOtter](https://swiftotter.com/certifications#/) - 💰



## Magento Blog





## Resources

- [Best Practices – Magento Help Center](https://support.magento.com/hc/en-us/categories/360002582351-Best-Practices-)
- [Security Best Practices | Magento 2.4 User Guide](https://docs.magento.com/user-guide/magento/magento-security-best-practices.html)
- [Magento Security News | Best Practices](https://magento.com/security/best-practices)
- [Adobe-Magento-Commerce-Best-Practices-Guide.pdf](https://www.adobe.com/content/dam/cc/en/security/pdfs/Adobe-Magento-Commerce-Best-Practices-Guide.pdf)
- [talesh/magento-security-checklist](https://github.com/talesh/magento-security-checklist): A Magento community sourced security pre-flight checklist.
- [Performance Best Practices | Magento 2 Developer Documentation](https://devdocs.magento.com/guides/v2.4/performance-best-practices/introduction.html)
- [Best Practices for Extension Developers | Magento 2 Developer Documentation](https://devdocs.magento.com/guides/v2.4/ext-best-practices/bk-ext-best-practices.html)
- [Cloud Knowledge Base and Support](https://support.magento.com/)
- [Complete Guide on Magento 2 Development Best Practices](https://www.icecubedigital.com/blog/complete-guide-on-magento-2-development-best-practices/)
- [Awesome Magento 2](https://github.com/DavidLambauer/awesome-magento2) - DavidLambauer/awesome-magento2



# 4 ~~Cosas~~ *Cosicas* <br/> Sobre Tus Servicios Favoritos

![Basílica del Pilar](images/basilica.png)

::: Notes   

Based on [Zaragoza and Ebro view from the highest tower, by Gregorio Puga Bailón from A Coruña, https://commons.wikimedia.org/wiki/File:Zaragoza_and_Ebro_view_from_the_highest_tower.jpg](https://upload.wikimedia.org/wikipedia/commons/f/f9/Zaragoza_and_Ebro_view_from_the_highest_tower.jpg)

:::

[](.hidden-title)

### AWS Services

![AWS icons](images/aws-icons.png)

::: Notes

240 different services.

what the heck is AWS Service Management Connector.

:::

[](#the-good-things,.coverbg)

### The good things

![](images/architecture.png)

## Storage

![Based on Black Internal Hdd on Black Surface, by Orientation, https://www.pexels.com/photo/black-internal-hdd-on-black-surface-117729/](images/hard-drive.png)

[](.illustration.partial)

### Superb Service Storage (S3)

![S3 icon](images/amazon-s3-simple-storage-service.svg)

* 🪦 Requiem for Select 🪦
* 🦁 Wild wirecards 🦁
* ⚡ Faster ⚡
* ⛰️ Higher ⛰️
* 💪 Stronger 💪
* 🤝 Together 🤝

::: Notes

Talk about spending the afternoon watching curling games on the Olympics.

* All good free things become to and end, and Select will not be available for new accounts
* Do the wild **wirecards demo**. 
    - **S3 Inventory** generates flat files with objects and metadata.
    - **S3 Metadata** provides live inventory table updated each hour, plus journal with events. All searcheable.
* Faster: do the **parallel upload demo** and talk about multipart
* Higher: from 5TB to **50TB**. Still, rewrite it all for changing one byte
* Stronger: **strong consistency** on all regions allows things like S3 Mount
* Together: **Amazon S3 Multi-Region Access Points** (MRAPs) provide a single global endpoint to route S3 requests across replicated buckets in multiple AWS Regions

:::*

[](.illustration)

### DynamoDB + SQL

![DynamoDB icon](images/amazon-dynamodb.svg)

PartiQL is a SQL-compatible query language for querying and analyzing semi-structured data across AWS services like DynamoDB, S3, and Redshift.

There will be dragons🐉

::: Notes

* DynamoDB native queries are the worst, after Mongo's
* SQL is handy, do the demo
* Sawbuck is a reindeer with **flowers on its antlers**. So cool.
* BEWARE OF **JOINS AND SCANS**

:::

[](#rds-aurora,.illustration.partial)

### RDS (Aurora)

![Aurora icon](images/amazon-aurora.svg)

* 🧬🧬 **Fast cloning** with copy-on-write snapshots for dev and reporting 
* ⌯⌲ **Local write forwarding** on replicas eliminates dual endpoints with 20ms latency
* 💸💱 **Aurora I/O-Optimized** for stop worrying about IOPS

::: Notes

* Fast cloning: 
    * copy-on-write
    * 5 min for starting up the instance
    * Awesome for reporting or dev. 
    * Storage payed for updated pages
* Local write forwarding:
    * Started in Aurora Global
    * Extended to local replica nodes
    * No need to keep two endpoints
    * Isolation/Lock considerations
    * 20ms latency
    * Joke about "Too much? well, if you go n+1 with hibernate..."
* Aurora I/O-Optimized
    * Increases instance+storage cost and elliminates IOPS
    * Useful when IOPS > 25% of the bill 
:::

## Compute

![Based on Four Pistons With Connecting Rods on Engine, by Orientation, https://www.pexels.com/photo/four-pistons-with-connecting-rods-on-engine-190539/](images/engine.png)

[](#asg,.illustration.partial)

### Autoscaling Groups

![ASG icon](images/amazon-asg.svg)

* 🐦‍🔥 Maximum Instance Lifetime
* ✨ Capacity rebalancing
* 🌡️ Warm Pool
* ♻️ EC2 Instance Refresh

::: Notes

* Max lifetime: includes availability budgets.
* Capacity rebalancing: replaces spot in risk of being reclaimed
* Warm pool: super cool for windows.
* Instance refresh: demo!

:::

## Traffic

![Based on Aerial Photography Of Cars On The Road, by Arnie Chou, https://www.pexels.com/photo/aerial-photography-of-cars-on-the-road-1877271/](images/traffic.png)

[](#alb,.illustration.partial)

![ALB icon](images/Application-Load-Balancer.svg)

* OIDC-compliant authentication
* Least_outstanding_requests connections
* Health thresholds

::: Notes

* Authentication
    * Works with Entra ID, Cognito, etc
    * Signs a JWT with ALB's key 
    * Contains groups in claim
* Least oustanding
    * Will help nodes that accumlate longer requests
    * Nodes can run an agent for managing weights
* Health thresholds
    * Percentage of healthy instances
    * Integrates with R53
    * Automatic failover on R53


:::


[](#cloudfront,.illustration.partial)

### CloudFront

![Cloudfront icon](images/amazon-cloudfront.svg)

* 🔵🟢Staging distributions
* 🏗️ Continous deployment

::: Notes

* Two distributions: main and staging
* Up to 15% of the traffic to staging
* Supports sticky distributions
* A/B, Canary...

:::

[](#javi,.illustration)

### github.com/ciberado

![Javi](images/javi.png)

* [linkedin.com/in/javier-more](linkedin.com/in/javier-more)
* [javi.moreno@global.ntt](mailto:javi.moreno@global.ntt)
* [javi.moreno@aprender.cloud](mailto:javi.moreno@aprender.cloud)

![QR Code to LinkedIn](images/qr-code.svg)

# Ch 2. Elasticsearch in the VSF context

In this chapter, we are going to cover : 
[[toc]]

## 0. Introduction
_Elasticsearch_ is the choice of _Vue Storefront_ for its data store as naturally as there must be reasons behind this. 
By its name, you can deduce _Elastic_ to mean scalable, extendable, distributed and type-agnostic which is great in this big data era while _Search_ implies indexing, filter, _Read_ among _CRUD_ which shows its focus. So far so good, then, what is all this fuss about Elasticsearch? 

_Elasticsearch_ is a full-text search and analysis engine based on _Apache Lucene_ by its definition. It employs inverted index which means _documents_ are indexed via all the unique _terms_ occurred and the ability to take advantage of assembling the per-field data structures can explain why _Elasticsearch_ is ultrafast. 

The other strong point is, notably, it's inborn distributed. Experience from a single node elasticsearch and multiple clusters of it is almost identical and doing so is painless as it works out of the box. There are virtually tons of points to make for why _Elasticsearch_ is your elected mid-stop between datahouse and storefront. Now let's move on to how it's implemented in _Vue Storefront_. 

_Vue Storefront_ defines itself backend-agnostic PWA e-commerce solution where _Vue Storefront_ is a storefront as the name dictates, and _Elasticsearch_ works as a datastore for _catalog_ and its sibling data such as _taxrule_, _products_ and so on. When a storefront requests information about a product, then it fetches _index_ of _documents_ about the _term_ queried from _Elasticsearch_ without traversing it to the source web store (be it Magento) so it skips all the heavy loading of the store whose database behind also is not concered which makes customers happy for pleasant experience. 

Without much further ado, let's see what's served as an appetizer :)
<br />
<br />

## Appetizer. Where Elasticsearch fits in VSF

<br />
<br />

## 1. Set Elasticsearch up and running for VSF
### 1. Preparation
### 2. Recipe
### 3. Peep into the kitchen (what happens internally)
### 4. Chef's secret (protip)
<br />
<br />

## 2. _Elastic_ and _Search_ in VSF
### 1. Preparation
### 2. Recipe
### 3. Peep into the kitchen (what happens internally)
### 4. Chef's secret (protip)
<br />
<br />

## 3. Now ES7 is also supported in VSF
_Elasticsearch_ has been under massive upgrade with interval so intense as only two weeks exist between release of `6.7` and `7.0`. Can you feel the heat of the community? While we can enjoy the improvement and enhancement of the _Elastic Stack_, there is a list to check before smooth upgrade. And it also works just the same way as you need to fix _Vue Storefront_ stack for compatibility with _Elasticsearch 7.x_. 

As _Vue Storefront_ stack is mostly associated with _Elasticsearch_ through _Vue Storefront API_, you should fix files for _Vue Storefront API_ along with a few callers for it from _Vue Storefront_. However, most changes take place in core parts of the platform on purpose so your labor will have been minimized for your inner peace. Still, _configs_ and/or _migration_ need fixes where it's necessary. This recipe walks you through how to do it one by one. 


### 1. Preparation
 - You need to have [setup _Vue Storefront_ stack](setup) including _Vue Storefront API_. 
 - ES7 is supported from _Vue Storefront_ version `1.11` and up. You should have it accordingly. 
 - ES7 is supported from _Vue Storefront API_ version `1.11` and up. You should have it accordingly too. 
 - ES7 is supported from _mage2vuestorefront_ with branch `feature/es7`. You should have it too.  
:::tip NOTE
How to download the latest `1.11` via `git` is explained in [_Protip_](#secret-1-how-to-upgrade-to-1-11-technically-foolproof-approach)
:::


### 2. Recipe
 0. You should fix `docker-compose.nodejs.yml` file as linked _Elasticsearch_ container should be updated like below : 

<div id="d-nodejs-yml">

</div>
<script>
var dNodejsYml = Diff2Html.getPrettyHtml(
  '--- a/docker-compose.nodejs.yml\n+++ b/docker-compose.nodejs.yml\n@@ -6,7 +6,7 @@ services:\n       context: .\n       dockerfile: docker/vue-storefront-api/Dockerfile\n     depends_on:\n-      - es1\n+      - es7\n       - redis\n     env_file: docker/vue-storefront-api/default.env\n     environment:\n',
  {inputFormat: 'diff', showFiles: false, matching: 'none', outputFormat: 'line-by-line'}
);
document.getElementById('d-nodejs-yml').innerHTML = dNodejsYml;
</script>


 1. `docker-compose` for _Elasticsearch 7_ is included in `1.11`. Let's run the docker container for _Elasticsearch 7_ from **Vue Storefront API root path** as follows : 
 ```bash
docker-compose -f docker-compose.elastic7.yml -f docker-compose.nodejs.yml up
 ```
:::tip TIP
You can run it in the _detach_ mode with option flag `-d` as in 
 ```bash
docker-compose -f docker-compose.elastic7.yml -f docker-compose.nodejs.yml up -d
 ```
:::

 2. You will see the screen as below : 
 ```bash
Starting es7 ... 
Starting vuestorefrontapi_redis_1 ... 
Starting vuestorefrontapi_redis_1
Starting vuestorefrontapi_redis_1 ... done
Attaching to es7, vuestorefrontapi_redis_1
es7      | OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
redis_1  | 1:C 23 Dec 18:00:28.554 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_1  | 1:C 23 Dec 18:00:28.554 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1, just started
redis_1  | 1:C 23 Dec 18:00:28.554 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis_1  | 1:M 23 Dec 18:00:28.555 * Running mode=standalone, port=6379.
redis_1  | 1:M 23 Dec 18:00:28.555 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis_1  | 1:M 23 Dec 18:00:28.555 # Server initialized
redis_1  | 1:M 23 Dec 18:00:28.556 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
redis_1  | 1:M 23 Dec 18:00:28.556 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
redis_1  | 1:M 23 Dec 18:00:28.556 * DB loaded from disk: 0.000 seconds
redis_1  | 1:M 23 Dec 18:00:28.556 * Ready to accept connections
es7      | {"type": "server", "timestamp": "2019-12-23T18:00:30,129+0000", "level": "INFO", "component": "o.e.e.NodeEnvironment", "cluster.name": "docker-cluster", "node.name": "be374d24f82e",  "message": "using [1] data paths, mounts [[/ (overlay)]], net usable_space [149.4gb], net total_space [250.9gb], types [overlay]"  }
es7      | {"type": "server", "timestamp": "2019-12-23T18:00:30,133+0000", "level": "INFO", "component": "o.e.e.NodeEnvironment", "cluster.name": "docker-cluster", "node.name": "be374d24f82e",  "message": "heap size [494.9mb], compressed ordinary object pointers [true]"  }
es7      | {"type": "server", "timestamp": "2019-12-23T18:00:30,135+0000", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "be374d24f82e",  "message": "node name [be374d24f82e], node ID [e8P_hrouSEKIWnylBaelVw], cluster name [docker-cluster]"  }
# abridged ...
 ```
 :vhs: You may also watch it in [bash playback :movie_camera:](https://asciinema.org/a/NcfdFuMkJ5LWzVbgb7m35coOV)

 You might notice the script spawns two containers, one of which is for `redis` while the other is for `elasticsearch 7`. (`kibana` container is optional from `1.11`)

 3. Visit `localhost:9200` from your browser then it should print likewise as follows :
```text
{
  "name" : "be374d24f82e",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "3Gk6anHkQU--5TmenJkdrw",
  "version" : {
    "number" : "7.3.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "1c1faf1",
    "build_date" : "2019-09-06T14:40:30.409026Z",
    "build_snapshot" : false,
    "lucene_version" : "8.1.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

 4. Fix `local.json` to update configuration for `indexTypes` and `apiVersion` under `elasticsearch` as follows :

<div id="d-local-json">

</div>
<script>
var dLocalJson = Diff2Html.getPrettyHtml(
  '--- a/config/local.json\n+++ b/config/local.json\n@@ -2,8 +2,6 @@\n     \"host\": \"localhost\",\n     \"port\": 9200,\n     \"protocol\": \"http\",\n-    \"user\": \"elastic\",\n-    \"password\": \"changeme\",\n     \"min_score\": 0.01,\n     \"indices\": [\n       \"vue_storefront_catalog\",\n@@ -13,10 +11,11 @@\n     \"indexTypes\": [\n       \"product\",\n       \"category\",\n-      \"cms\",\n+      \"cms_block\",\n+      \"cms_page\",\n       \"attribute\",\n       \"taxrule\",\n       \"review\"\n     ],\n-    \"apiVersion\": \"5.6\"\n-  }, \n+    \"apiVersion\": \"7.1\"\n+  }, ',
  {inputFormat: 'diff', showFiles: false, matching: 'none', outputFormat: 'line-by-line'}
);
document.getElementById('d-local-json').innerHTML = dLocalJson;
</script>


 5. Once _Elasticsearch 7_ instance is up and running, then run the new script which creates index with the proper data types of fields applied.
```bash
yarn db7 new
```

This is because you should newly put mapping for _Elasticsearch 7_ which only allows one _document_ per single _index_. [more info](https://www.elastic.co/guide/en/elasticsearch/reference/current/breaking-changes-7.0.html)

 The screen spits log as follows : 
```bash
yarn run v1.21.1
$ node scripts/db7.js new
** Hello! I am going to create NEW ES index
Public index alias does not exists aliases_not_found_exception
Public index alias does not exists aliases_not_found_exception
Public index alias does not exists aliases_not_found_exception
Public index alias does not exists aliases_not_found_exception
Public index alias does not exists aliases_not_found_exception
Public index alias does not exists aliases_not_found_exception
Public index alias does not exists aliases_not_found_exception
Done in 2.27s.
```
 :vhs: You may also watch it in [bash playback :movie_camera:](https://asciinema.org/a/UErONnmqK1m2EFNkWRrG0E6p4)

Don't worry about `aliases_not_found_exception`. It simply means it failed to cleanse the orphaned aliases since there was none to delete in the first place.

 6. Check if the mapping has been created successfully from the terminal against Elasticsearch API : 
```bash
curl -XGET 'http://localhost:9200/_mapping?pretty=true'
```

 Result should be shown as : 
```bash
{
  "vue_storefront_catalog_cms_block" : {
    "mappings" : {
      "properties" : {
        "creation_time" : {
          "type" : "date",
          "format" : "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
        },
        "id" : {
          "type" : "long"
        },
        "identifier" : {
          "type" : "keyword"
        },
        "update_time" : {
          "type" : "date",
          "format" : "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
        }
      }
    }
  },
  "vue_storefront_catalog_review" : {
    "mappings" : { }
  },
  "vue_storefront_catalog_taxrule" : {
    "mappings" : {
      "properties" : {
        "id" : {
          "type" : "long"
        },
        "rates" : {
          "properties" : {
            "rate" : {
              "type" : "float"
            }
          }
        }
      }
    }
  },

# ... abridged ...

  "vue_storefront_catalog_category" : {
    "mappings" : {
      "properties" : {
        "created_at" : {
          "type" : "date",
          "format" : "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
        },
        "is_active" : {
          "type" : "boolean"
        },
        "parent_id" : {
          "type" : "integer"
        },
        "position" : {
          "type" : "integer"
        },
        "product_count" : {
          "type" : "integer"
        },
        "slug" : {
          "type" : "keyword"
        },
        "updated_at" : {
          "type" : "date",
          "format" : "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
        },
        "url_key" : {
          "type" : "keyword"
        },
        "url_path" : {
          "type" : "keyword"
        }
      }
    }
  }
}
``` 
You can find each index has only its single mapping with convention `${indexName}_${entityType}`.

 7. Next thing is, pumping data from source web store to the newly created ES7 index. Go to _mage2vuestorefront_ directory and fix `apiVersion` inside `elasticsearch` node in `config.js`.

<div id="d-config-js">

</div>
<script>
var dConfigJs = Diff2Html.getPrettyHtml(
  '--- a/src/config.js\n+++ b/src/config.js\n@@ -61,7 +61,7 @@ module.exports = {\n   },\n \n   elasticsearch: {\n-    apiVersion: process.env.ELASTICSEARCH_API_VERSION || \'5.6\'\n+    apiVersion: process.env.ELASTICSEARCH_API_VERSION || \'7.1\' \n   },\n \n   redis: {\n',
  {inputFormat: 'diff', showFiles: false, matching: 'none', outputFormat: 'line-by-line'}
);
document.getElementById('d-config-js').innerHTML = dConfigJs;
</script>

 With this change in `config.js`, `mage2vuestorefront` knows how to deal with your _Elasticsearch_ whose version is higher than `6`. 

 8. Now teach the machine your configuration using shell `ENV` as following example : 
 ```bash
 export TIME_TO_EXIT=2000
 export MAGENTO_CONSUMER_KEY=lv1unkldzkcex68l3eojut4j66qqho8w
 export MAGENTO_CONSUMER_SECRET=zhkuqvweo0bsg14noujqje49x3wht0qr
 export MAGENTO_ACCESS_TOKEN=z6ftgc5005212bc6lnszxa7d7ocl8hgc
 export MAGENTO_ACCESS_TOKEN_SECRET=h8tikjq9sz7tqm6hyhdfgs96krb6qzyk
 
 export MAGENTO_URL=http://local.magento/rest # Replace the url with your Magento 2 URL
 export INDEX_NAME=vue_storefront_catalog # This will be the base name of the index we use
 ```
 Type them in a shell then your `ENV` remembers the variables until the session expires. 
 
 :::tip TIP
 If you don't know how to get those credentials, please take a look at [data import](/guide/cookbook/data-import.html#_2-2-recipe-b-using-on-premise)
 :::
 
 9. Run your worker to pump it up from source web store to Elasticsearch : 
```bash
node --harmony cli.js categories --removeNonExistent=true
```

Screen will show you logs as follows : 
```bash
2020-01-07T07:21:00.959Z - debug: Elasticsearch module initialized!
info: Winston logging library initialized.
2020-01-07T07:21:00.991Z - info: Connected correctly to server
2020-01-07T07:21:00.992Z - info: TRANSACTION KEY = 1578381660987
debug: Calling API endpoint: GET http://franko.local/rest/V1/categories
debug: Response received.
Dest. cat path =  default-category-2
debug: Calling API endpoint: GET http://franko.local/rest/V1/categories/2
debug: Response received.
Dest. cat path =  default-category-2
2020-01-07T07:21:02.029Z - debug: Storing extended category data to cache under: vue_storefront_catalog_cat_2
debug: Calling API endpoint: GET http://franko.local/rest/V1/categories/4
debug: Calling API endpoint: GET http://franko.local/rest/V1/categories/5
debug: Calling API endpoint: GET http://franko.local/rest/V1/categories/3
debug: Calling API endpoint: GET http://franko.local/rest/V1/categories/7
debug: Calling API endpoint: GET http://franko.local/rest/V1/categories/8
debug: Calling API endpoint: GET http://franko.local/rest/V1/categories/9
debug: Calling API endpoint: GET http://franko.local/rest/V1/categories/6
debug: Response received.
Dest. cat path =  boy/pants/pants-4
2020-01-07T07:21:03.427Z - info: Subcategory data extended for 2, children object 4
debug: Response received.
Dest. cat path =  girl/pants/pants-9
2020-01-07T07:21:03.431Z - info: Subcategory data extended for 2, children object 9

# abridged ...
```
:vhs: You may also watch it in [bash playback :movie_camera:](https://asciinema.org/a/IViPWiFBkiE4of9L3ykncoPmU)

Confirm you are on the correct path with following command :
```bash
curl -XGET "http://localhost:9200/vue_storefront_catalog_category/_search?pretty=true"
```

Response should be like below : 
```bash
# ... abridged
      {
        "_index" : "vue_storefront_catalog_category",
        "_type" : "_doc",
        "_id" : "8",
        "_score" : 1.0,
        "_source" : {
          "id" : 8,
          "parent_id" : 6,
          "name" : "Outer",
          "is_active" : true,
          "position" : 2,
          "level" : 3,
          "product_count" : 1,
          "children_data" : [ ],
          "children" : "",
          "created_at" : "2019-12-24 17:05:27",
          "updated_at" : "2019-12-24 17:05:27",
          "path" : "1/2/6/8",
          "available_sort_by" : [ ],
          "include_in_menu" : true,
          "display_mode" : "PRODUCTS",
          "is_anchor" : "1",
          "children_count" : "0",
          "custom_use_parent_settings" : "0",
          "custom_apply_to_products" : "0",
          "url_key" : "outer-8",
          "url_path" : "girl/outer/outer-8",
          "slug" : "outer-8",
          "tsk" : 1578381660987
        }
      },
      {
        "_index" : "vue_storefront_catalog_category",
        "_type" : "_doc",
        "_id" : "9",
        "_score" : 1.0,
        "_source" : {
          "id" : 9,
          "parent_id" : 6,
          "name" : "Pants",
          "is_active" : true,
          "position" : 3,
          "level" : 3,
          "product_count" : 0,
          "children_data" : [ ],
          "children" : "",
          "created_at" : "2019-12-24 17:05:52",
          "updated_at" : "2019-12-24 17:05:52",
          "path" : "1/2/6/9",
          "available_sort_by" : [ ],
          "include_in_menu" : true,
          "display_mode" : "PRODUCTS",
          "is_anchor" : "1",
          "children_count" : "0",
          "custom_use_parent_settings" : "0",
          "custom_apply_to_products" : "0",
          "url_key" : "pants-9",
          "url_path" : "girl/pants/pants-9",
          "slug" : "pants-9",
          "tsk" : 1578381660987
        }
      },
      {
        "_index" : "vue_storefront_catalog_category",
        "_type" : "_doc",
        "_id" : "7",
        "_score" : 1.0,
        "_source" : {
          "id" : 7,
          "parent_id" : 6,
          "name" : "Skirt",
          "is_active" : true,
          "position" : 1,
          "level" : 3,
          "product_count" : 1,
          "children_data" : [ ],
          "children" : "",
          "created_at" : "2019-12-24 17:05:13",
          "updated_at" : "2019-12-24 17:26:22",
          "path" : "1/2/6/7",
          "available_sort_by" : [ ],
          "include_in_menu" : true,
          "display_mode" : "PRODUCTS",
          "is_anchor" : "1",
          "children_count" : "0",
          "custom_use_parent_settings" : "0",
          "custom_apply_to_products" : "0",
          "url_key" : "skirt-7",
          "url_path" : "girl/skirt/skirt-7",
          "slug" : "skirt-7",
          "tsk" : 1578381660987
        }
      }
    ]
  }
}

```

10. Repeat this process until you are done importing entities with all the indices you mapped to _Elasticsearch_ before such as _products_, _attributes_, _taxrules_ and more as follows : 
```bash
node --harmony cli.js productcategories
node --harmony cli.js attributes --removeNonExistent=true
node --harmony cli.js taxrule --removeNonExistent=true
node --harmony cli.js products --removeNonExistent=true --partitions=1
node --harmony cli.js reviews
node --harmony cli.js blocks
node --harmony cli.js pages
```

:::tip NOTE
API endpoints for _reviews_, _blocks_ and _pages_ from Magento are not available out of the box. You should install additional modules for them on your own. [review](https://github.com/DivanteLtd/magento2-review-api), [cms](https://github.com/SnowdogApps/magento2-cms-api)
:::

Now you are ready to launch your Vue Storefront shop powered by _Elasticsearch 7_. 

11. Run the following to launch your shop at _Vue Storefront_ root path.
```sh
docker-compose up 
```
You are all good to go!


### 3. Peep into the kitchen (what happens internally)
_Elasticsearch_ has been through intensive updates ceaselessly. Mostly, it's gradual changes with backward compatibility so it won't have a big impact even if you upgrade one to another. This time, however, upgrade to `7.x` has a few breaking changes, one notably of which is, multiple `_type` is not allowed for an index. 

Fixing breaks for _Elasticsearch 7.x_ has been successfully included in _Vue Storefront 1.11_ upgrade. 

First thing you need to do is, download the upgrade with `git`. Upgraded version of containers are described in `docker-compose` files so what it contains inside is well encapsulated that you don't have to be concerned of how it works internally unless you want to tweak customization more than the default. 

Next, you should newly create each mapping for _Elasticsearch_ index as per the type. Then pumping your data into _Elasticsearch_ using _mage2vuestorefront_.  

If things go as planned so far so good, your above-7-Elasticsearch should serve as performant search engine for data store with enhanced indexing ability.  

### 4. Chef's secret (protip)

#### Secret 1. How to upgrade to `1.11` technically? foolproof approach.
There are 3 repositories that should be upgraded for `1.11`. 
:::warning CHECK
Please make sure your git space is clean and has nothing to conflict with upcoming merge. 
:::

First off, go to ___Vue Storefront API___ folder and type the following : 
```sh 
git fetch
git merge v1.11.0
```
:::tip NOTE
Please note `v1.11.0` is the _tag_ that denotes the final commit for the version at the moment.  
:::

Next, go to ___Vue Storefront___ folder and type the following : 
```sh
git fetch
git merge v1.11.0
```

Finally, go to ___mage2vuestorefront___ folder and run the following commands :
```sh
git fetch
git merge origin/feature/es7
```

Now you are all set :) 

<!--#### Secret 2. How to install `1.11` technically? foolproof approach. -->

<br />
<br />

## 4. Extend Elasticsearch entities for VSF
Online shops normally have certain types of models and scenarios in common. (Because shops are shops in the end! What do you expect from shops? ;)) They are well known to the community and most of e-commerce software already implemented them into their frameworks as expected which is good. Those are represented as entities, namely, _Catalog_, _Customer_, _Order_, _Promotion_ and more. Since _Vue Storefront_ functions as the gorgeous gateway to those e-commerce backend, it also needs to mirror those entities as smooth as possible.  

 The large part of main entities are already implemented in VSF `core` as expected but you might still need to add or remove additional entities as you want it to fulfill your mission. This recipe will give you an idea of how to do it. 

### 1. Preparation


### 2. Recipe
### 3. Peep into the kitchen (what happens internally)
### 4. Chef's secret (protip)
<br />
<br />


## 5. VSF Configuration pertaining to Elasticsearch
There is a handful of configuration you can tweak in `local.json` in order to modify how _Vue Storefront_ behaves in conjunction with _Elasticsearch_. 

### 1. Preparation
### 2. Recipe
### 3. Peep into the kitchen (what happens internally)
### 4. Chef's secret (protip)
<br />
<br />


## 6. Peripheral Tools available for Elasticsearch
### 1. Preparation
### 2. Recipe
### 3. Peep into the kitchen (what happens internally)
### 4. Chef's secret (protip)
<br />
<br />

<!-- 
## 6. 
### 1. Preparation
### 2. Recipe
### 3. Peep into the kitchen (what happens internally)
### 4. Chef's secret (protip)
<br />
<br /> -->

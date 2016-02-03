# Ansible playbook to perform a rolling restart of an Elasticsearch cluster
This playbook will perform a rolling restart of an Elasticsearch cluster based on the elastic [best practice.](https://www.elastic.co/guide/en/elasticsearch/guide/master/_rolling_restarts.html) 
Additionally it is possible to perform a [synced flush](https://www.elastic.co/guide/en/elasticsearch/reference/2.2/indices-synced-flush.html) before stopping the service to ensure all data has 
been persisted to disc. In the default configuration the playbook will fail if any
shards return an error during the synced flush.
The playbook is easily extended to perform mainenance tasks like upgrades as part of the rolling restart.

##Installation
Simply clone the repository to a location of your choice.

```bash
$ git clone git@github.com:soenkeliebau/es_rollingrestart.git
```

##Usage
Run the playbook against an inventory containing the elasticsearch hosts that you want to restart. You can either pass your hosts directly on the command line or create
an [inventory file](http://docs.ansible.com/ansible/intro_inventory.html) that contains your hosts.

```bash
# Inventory passed on command line (mind the trailing colon, its important!)
$ ansible-playbook playbook.yml -u <user> -s --private-key </path/to/keyfile> -i 10.0.0.112,10.0.0.113,

# Inventory from file
$ ansible-playbook playbook.yml -u <user> -s --private-key </path/to/keyfile> -i </path/to/inventory/file>
```
By default the playbook will perform a synced flush and fail if not all shards report a successful flush. This can either be changed directly in the playbook.yml by configuring the two variables
* perform_flush
* ignore_failed_flush

You can also override these from the command line as necessary by passing the -e parameter. If you wanted to ignore a failed flush for just this run, because you couldn't stop indexing for some
valid reason you'd use the following:

```bash
# Ignore failed flush for this run regardless of setting in playbook.yml
$ ansible-playbook playbook.yml -e "ignore_failed_flush=true" -u <user> -s --private-key </path/to/keyfile> -i </path/to/inventory/file>
```

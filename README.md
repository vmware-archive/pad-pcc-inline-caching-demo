# pcc-inline-caching-demo

This demo is used to show how inline cache pattern is used in Pivotal Cloud Cache and MongoDB.

## Installing

### Step 1 - Connect GFSH with PCC Cluster

```
gfsh> connect --use-http=true --url=<pcc-gfsh-url> --user=<username> --password=<password>
```

### Step 2 - Deploy Server Jar using GFSH

```
gfsh> deploy --jar=<caching-server-jar-path>

gfsh> configure pdx --auto-serializable-classes=.* --disk-store=DEFAULT
```
### Step 3 - Reboot PCC Servers

```
$ cf update-service <your-pcc-service-name> -c '{"restart": true}'
```
### Step 4 - Create Region and AsyncQueue

```
gfsh> create region --name=Transactions --type=PARTITION --async-event-queue-id=item-writebehind-queue

gfsh> create async-event-queue --listener=io.pivotal.event.writebehind.ItemAsyncEventListener --id=item-writebehind-queue --batch-size=10 --batch-time-interval="20" --parallel="false" --dispatcher-threads=1 --listener-param=mongoAddress#xx.xx.xx.xx,mongoPort#27017,dbName#test,collectionName#test
```

### Step 5 - Deploy DataLoader Client App

Follow [DataLoader](https://github.com/Pivotal-Field-Engineering/pad-pcc-dataloader) instructions to deploy the app. Note that you don't have to recreate `Transactions` region.

## Use Demo

Use `DataLoader` app to start loading data. Check remote MongoDB server to see if data goes through.

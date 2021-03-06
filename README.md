# gotq

golang distribute task queue, base on redis, lightweight and easy use, inspired by python's celery

![](https://raw.githubusercontent.com/shidenggui/assets/master/gotq/example_worker.png)

## Feature

* use redis as broker
* customize worker numbers
* support async task or block for wait reply
* worker will discard outdate sync task

## Installation

```golang
go get github.com/shidenggui/gotq
```

## Overview

you can see example in `gotq/example`

### start worker

```golang
go run example/worker/worker.go
```

### dispatch task

#### block for result
```golang
go run example/server/server.go
```

#### dispatch async task

```golang
go run example/server/main.go -m delay
```

## QuickStart

### define task
,
```golang
//config broker
var cfg = config.Config{
	Broker: &config.BrokerCfg{
		Host:     "127.0.0.1",
		Port:     6379,
		Password: "",
		DB:       0,
	},
}

var App = gotq.New(&cfg)

// taskSender, one task function one sender
var AddSender = App.Register(Add)

type AddArgs struct {
	X int64
	Y int64
}

type AddResult struct {
	Sum int64
}

func Add(argsInter map[string]interface{}) map[string]interface{} {
	args := new(AddArgs)

	//recommended use mapstructure to bind map to struct
	ms.Decode(argsInter, args)
    
    // do soemthing you want

    //recommeded use github.com/fatih/strtuts to convert map to struct
    res := &AddResult{
        Sum: args.X + args.Y,
    }

    /* also can simple use map[string]interface{}
	res := make(map[string]interface{})
	res["sum"] = args.X + args.Y
    /*

	return structs.Map(res)
}

```

### start workers

```
workerNum := int64(500)
App.WorkerStart(workerNum)
```

### dispatch task

#### Async task dispatch

```golang
args := &AddArgs{3, 4}
err := AddSender.Delay(args)
```

#### Sync task dispatch, block for wait result

```golang
args := &AddArgs{3, 4}
timeout := int64(5)
res, err := AddSender.Request(args, timeout)
...
//recommended use mapstructure to bind map to struct
rep := new(AddReply)
ms.Decode(res, rep)
```

---
title: Cluster
---

By default Prism runs in single-threaded mode. To use Node's cluster mode, specify the --cluster option when starting Prism.

```sh
prism -p $PORT -c $CONFIG --cluster
```

By default, Prism will start as many workers as there are CPUs on the machine (recommended). To specify this explicitly use the --workers option.

```sh
prism -p $PORT -c $CONFIG --cluster --workers 8
```

Worker count can also be specified in the config file via the 'workers' property.

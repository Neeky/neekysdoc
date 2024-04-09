## 查询当前有哪些集群
```bash
orchestrator-client -c clusters
git.sqlpy.com:4406

```

## 查询指定集群的拓扑
```bash
orchestrator-client -c topology -i git.sqlpy.com:4406
git.sqlpy.com:4406     [0s,ok,8.0.33,rw,ROW,>>,GTID,semi:master]
+ git.sqlpy.com:4407   [0s,ok,8.0.33,ro,ROW,>>,GTID,semi:replica]
  + git.sqlpy.com:4408 [0s,ok,8.0.33,ro,ROW,>>,GTID,semi:replica]
```

## 调整复制的层级结构
```bash
orchestrator-client -c relocate -i git.sqlpy.com:4408 -d git.sqlpy.com:4406
git.sqlpy.com:4408<git.sqlpy.com:4406

orchestrator-client -c topology -i git.sqlpy.com:4406
git.sqlpy.com:4406   [0s,ok,8.0.33,rw,ROW,>>,GTID,semi:master]
+ git.sqlpy.com:4407 [0s,ok,8.0.33,ro,ROW,>>,GTID,semi:replica]
+ git.sqlpy.com:4408 [0s,ok,8.0.33,ro,ROW,>>,GTID,semi:replica]

```

## 手工切换
```bash
orchestrator-client -c  graceful-master-takeover -i git.sqlpy.com:4407 -d git.sqlpy.com:4407

orchestrator-client -c topology -i git.sqlpy.com:4406
git.sqlpy.com:4407   [0s,ok,8.0.33,rw,ROW,>>,GTID]
- git.sqlpy.com:4406 [null,nonreplicating,8.0.33,ro,ROW,>>,GTID,semi:master,downtimed]
+ git.sqlpy.com:4408 [0s,ok,8.0.33,ro,ROW,>>,GTID,semi:replica]
```

---
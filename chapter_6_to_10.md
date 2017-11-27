# 6.如何通过IObjectPK pk获取实体对象

IObjectPK是一个表示实体对象id的对象，在日常开发工作中，经常需要通过它来获取实体对象。以下是通过IObjectPK获取实体对象代码的模版：

```Java
I*Info i*Info =  *Factory.getLocalInstance(ctx);
*Info info = i*Info.get*Info(pk);
// 以下示例，通过IObjectPK获取成本对象实体：CostObjectInfo
ICostObject  iCostInfo = CostObjectFactory.getLocalInstance(ctx);
CostObjectInfo coi =  iCostInfo.getCostObjectInfo(pk);

```


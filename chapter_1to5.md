# 1.二次开发后，EAS 的运行机制
## BOS视图目录说明：

- MMEAS：EAS解决方案目录
- metadata：二次开发元数据存放目录
- basemetas：标准产品元数据存放目录
> 在BOS开发平台中，当在basemetas目录下双击某一个元数据文件时，BOS会自动帮我们生成该元数据的一份拷贝到metadata目录下，二次开发如果需要修改元数据，就到metadata目录下修改。 EAS在运行时，会优先加载metadata目录下的元数据。（注：若删除metadata目录下修改过的元数据，EAS会自动加载basemetas目录下标准产品的元数据，因此在开发过程中，如果metadata目录下的元数据文件改乱了，可大胆将其删除）。

## Java视图目录说明：

> 在BOS视图对元数据进行发布后，BOS平台会在`Java`视图生成相应的`.java`文件。此时，开发者可以通修改相关的`java`文件进行二次开发的工作。注意：二次开发所做的修改，要尽量避免与产品打补丁产生冲突。

# 2.UI类常用方法说明
- `onLoad()` 
> 说明：UI第一次加载时调用，通常用于单据第一次初始化时对单据进行相关属性的操作，注意，当在UI界面打开后，再点 “新增”按钮，此时是不执行此方法的。
- `actionAddNew_actionPerformed(ActionEvent arg0)`
> 说明：点击单据上的“新增”按钮时触发的事件。
- `verifyInput(ActionEvent e)` 
> 说明：验证输入内容
- `applyDefaultValue(IObjectValue iobjectvalue)`
> 说明：对表头的字段设置默认值时，重写此方法。
- `getSelectors()`
> 说明：暂缺。
- `*_dataChanged(DataChangeEvent e)`
> 说明：单据上某一字段的值改变时，触发此方法。
- `storeFields()` 
> 说明：暂缺。
- `actionRec_actionPerformed(ActionEvent e)`
> 说明：暂缺。

# 3.如何让表头的字段在单据提交后还能进行修改 
> 一般情况下，单据提交后，是不允许修改的。但是也存在一些特殊的情况，需要在工作流中，对单据中的某些字段进行修改，此时，一般是通过在自定义的 UI 扩展类中，重写`onLoad()`方法，然后在方法中调用控件的`setEnabled()`、`setEditable()`、`setReadOnly()`方法（这三个方法不一定需要全部调用，根据具体情况而定），代码如下： 
 
```Java
    @Override
    public void onLoad() throws Exception {
      super.onLoad();	
      // 单据未生成凭证之前，"收款类型"为可编辑、“往来户”也可编辑
      boolean hasFV = this.editData.isFiVouchered();
      if(!hasFV){
        // 收款类型
        f7RecBillType.setEnabled(true);
        f7RecBillType.setEditable(true);
        f7RecBillType.setReadOnly(false);
        // 往来户
        prmtPayer.setEnabled(true);
        prmtPayer.setEditable(true);
        prmtPayer.setReadOnly(false);
        }
    }
```

# 4.如何修改F7字段所关联的基础档案
> 在一般情况下，在 BIM 视图对单据新增F7字段时，就可以关联到想要的基础档案或其它视图。但是，当需要关联一些不存在业务单元（即`*.bizunit`文件）的基础档案时(如：自定义核算项目)。此时就只能通过以下步骤来处理： 
- 在 BIM 里加一个F7字段，关联物料或其他可以选到的基础资料。保存后 _先别发布_ 。
- 切换到 BOS 透视图，打到对应的`.relation`文件，修改`supplierEntity`，原来是指定物料的实体，改成自定义核算项目的实体。
- 打开 `XXXEditUI.ui`,修改F7字段的`queryInfo`，原来是指向物料的query，改成自定义核算项目的query

# 5.
> 当需要对F7控件所打个视图中的内容进行过滤时，可对F7控件设置过滤条件，使用方法如下:

```Java
    EntityViewInfo evi = new EntityViewInfo(); // 创建实体视图
    FilterInfo f = new FilterInfo(); // 创建过滤对象
    FilterItemInfo filter1 = new FilterItemInfo("number", "SHFL001", CompareType.GREATER); // 创建第一个过滤条件，第一个参数:所查询的实体的属性，第二个参数:属性的目标值，第三个参数：比较符
    FilterItemInfo filter2=  new FilterItemInfo("age", "20", CompareType.GREATER); // 创建第二个过滤条件
    f.getFilterItems().add(filter1); // 将过滤条件添加到过滤对象中
    f.getFilterItems().add(filter2);
    f.setMaskString("#0 or #1"); // 设置两个过滤条件之间的关系
    evi.setFilter(f); // 设置实体视图的过滤器
    f7.setEntityViewInfo(evi); // 将实体视图绑定到F7控件
    f7 .getQueryAgent().resetRuntimeEntityView();
```

[转载自呆呆的博客，有修改](https://my.oschina.net/hipanda/blog/703878) 
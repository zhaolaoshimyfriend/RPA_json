# 公共参数

本目录用于定义 **创建任务** 和 **任务执行** 的公共参数对象数据结构。

- 创建任务时的公共参数（在报文体顶层直接挂载，无 params 包装层）
- 任务执行后的公共返回结构（如 `result` 的通用字段）

---

## loginInfo.json（登录信息）

**用途**：作为多数任务的公共入参，用于向电子税务局执行登录或标识当前操作主体。平台在创建任务时，在报文体顶层传入 `loginInfo`；RPA 据此完成登录或切换企业/身份。

**报文路径**：`公共参数/loginInfo.json`

### 结构说明

| 字段 | 说明 |
|------|------|
| `areaCode` | 网报区编码 |
| `entrancePlatform` | 登录平台：0-电子税务局 |
| `loginPlatform` | 目标页面：0-电子税务局首页 |
| `loginType` | 登录方式：1-代理登录 |
| `agentTaxNo` | 代理登录方式时，此字段为登录需要输入的代理机构账号 |
| `userAccount` | 代理登录方式时，此字段为登录需要输入的账号 |
| `loginPassword` | 代理登录方式时，此字段为登录需要输入的密码 |
| `loginIdentity` | 登录身份：01-法定代表人，02-财务负责人，03-办税员，04-税务代理人（涉税服务人员），05-管理员，06-出口退税人员，07-领票人，08-社保经办人，09-开票员，10-销售人员，31-行政办事员，99-其他人员 |
| `companyName` | 被代理企业名称 |
| `companyTaxNo` | 被代理企业税号 |

### 使用方式

在报文体顶层挂载：

```json
{
  "taskType": "...",
  "taskId": "...",
  "loginInfo": {
    "areaCode": "210000",
    "entrancePlatform": "0",
    "loginPlatform": "0",
    "loginType": "1",
    "agentTaxNo": "...",
    "userAccount": "...",
    "loginPassword": "...",
    "loginIdentity": "04",
    "companyName": "...",
    "companyTaxNo": "..."
  },
  "业务参数": { ... },
  "result": { ... }
}
```

> 具体字段含义见 JSON 中的 `_fieldRemarks`。

---

## taskContext.json（任务上下文）

**用途**：描述任务执行所需的**企业信息**和**税款所属期信息**（如适用）。各类任务（申报、采集、核查等）均可携带此对象，以标识「向谁操作、针对哪个税款所属期」。

**报文路径**：`公共参数/taskContext.json`

### 结构说明

| 字段 | 说明 |
|------|------|
| `taxPeriodStart` | 税款所属期间起 |
| `taxPeriodEnd` | 税款所属期间止 |
| `taxpayerId` | 纳税人识别号 |
| `taxpayerName` | 纳税人名称 |
| `amountUnit` | 金额单位（如「人民币元(列至角分)」，部分申报表可选） |

### 使用方式

在报文体顶层挂载：

```json
{
  "taskType": "...",
  "taskId": "...",
  "loginInfo": { ... },
  "taskContext": {
    "taxPeriodStart": "2025-01-01",
    "taxPeriodEnd": "2025-12-31",
    "taxpayerId": "91370200MA3C123456",
    "taxpayerName": "青岛汉唐世纪信息技术有限公司",
    "amountUnit": "人民币元(列至角分)"
  },
  "业务参数": { ... },
  "result": { ... }
}
```

> 具体字段含义见 JSON 中的 `_fieldRemarks`。

---

## workflowSpec.json（工作流规格）

**用途**：描述 RPA 执行时要运行的工作流。平台在创建任务时，通过此对象指定 RPA 应执行的子步骤或子流程及依赖关系。

**报文路径**：`公共参数/workflowSpec.json`

### 结构说明

`workflowSpec` 为**列表**，每个元素表示一个子步骤/子流程，包含：

| 字段 | 说明 |
|------|------|
| `seqNo` | 编号 |
| `stepId` | 子任务id（唯一标识，被依赖时引用） |
| `stepName` | 子任务名称 |
| `dependsOn` | 依赖子任务（子任务 id 列表，本步骤执行前需先完成所列任务） |

### 示例（增值税一般纳税人申报）

```json
{
  "taskType": "...",
  "taskId": "...",
  "loginInfo": { ... },
  "workflowSpec": [
    { "seqNo": "1", "stepId": "invoiceSelection", "stepName": "发票勾选", "dependsOn": [] },
    { "seqNo": "2", "stepId": "statisticsConfirm", "stepName": "统计确认", "dependsOn": ["invoiceSelection"] },
    { "seqNo": "3", "stepId": "vatGeneralTaxDeclaration", "stepName": "纳税申报-增值税-一般纳税人", "dependsOn": ["invoiceSelection", "statisticsConfirm"] }
  ],
  "业务参数": { ... },
  "result": { ... }
}
```

> 具体字段含义见 JSON 中的 `_fieldRemarks`。

---

## 其他公共参数

后续可在此目录下补充更多公共参数定义。

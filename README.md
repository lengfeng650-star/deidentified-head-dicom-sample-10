---
license: Apache License 2.0
tags:
- DICOM
- 医学影像
- 头部影像
- 脱敏病例
- 病案首页
- medical-imaging
- de-identified
configs:
- config_name: default
  data_files:
  - split: train
    path: metadata/dicom_files_manifest.jsonl
---

# 脱敏头部 DICOM 影像病例样例 10 例

本数据集从 11 例已脱敏的头部影像病例包中选取 10 例整理开放。每例包含 DICOM 影像文件和对应的脱敏病案首页，适用于医学影像数据结构验证、DICOM 解析流程测试、影像与结构化病案信息关联建模、医疗数据产品演示等场景。

如需更大规模的医学影像、电子病历、药品说明书、中医古籍、医学考试题库等医疗数据合作，可发送邮件至 zhouhaoran@shujuyoupu.com。

## 数据组成

- 病例数：10
- 病例压缩包：10
- DICOM 文件数：1788
- 序列数：20
- 脱敏病案首页：10
- DICOM 文件体积：约 0.92 GB
- 文件级 Modality 统计：CT 1619；未匹配到 tag 侧车元数据 169

说明：源目录按头部影像样例交付；本次整理时从 DICOM tag 侧车文件抽取非敏感字段，统计结果显示已匹配文件以 CT 为主。使用时请以实际 DICOM 头信息为准。

## 目录结构

```text
data/
  case_archives/
    case_001.zip
    case_002.zip
    ...
metadata/
  dataset_summary.json
  archives_manifest.jsonl
  cases_manifest.jsonl
  dicom_files_manifest.jsonl
README.md
```

每个病例压缩包内保持如下结构：

```text
case_001/
  case_homepage.json
  dicom/
    series_001/
      *.dcm
    series_002/
      *.dcm
```

公开目录统一使用 case_001 到 case_010 命名，不保留源文件夹中的内部病例编号。病案首页中的内部脱敏编号也已替换为公开 case_id。

## Metadata 说明

metadata/archives_manifest.jsonl 为压缩包清单，记录每个病例 zip 的路径、DICOM 数量、序列数量、文件大小和 sha256。

metadata/dicom_files_manifest.jsonl 为文件级清单，主要字段包括：

| 字段 | 说明 |
|---|---|
| case_id | 公开病例编号 |
| series_id | 公开序列编号 |
| archive_path | 所在病例压缩包路径 |
| archive_internal_path | DICOM 文件在 zip 内的路径 |
| modality | 从 tag 侧车文件抽取的 Modality，未匹配时为 UNKNOWN |
| body_part_examined | 检查部位字段，如可用 |
| study_description | 检查描述字段，如可用 |
| series_description | 序列描述字段，如可用 |
| size_bytes | DICOM 文件大小 |
| sha256 | DICOM 文件哈希 |

metadata/cases_manifest.jsonl 为病例级清单，记录每例的压缩包路径、DICOM 数量、序列数量、病案首页在 zip 内的路径和体积统计。

## 病案首页字段

每例压缩包内的 case_homepage.json 为脱敏后的结构化病案首页，常见字段包括：

```json
{
  "case_id": "case_001",
  "patient_basic_info": {
    "gender": "男",
    "age": "77",
    "ethnicity": "汉族",
    "case_id": "case_001"
  },
  "diagnosis_info": {
    "primary_diagnosis": "诊断名称",
    "other_diagnoses": [],
    "icd10_codes": []
  },
  "treatment_info": {},
  "hospitalization_info": {}
}
```

## 加载示例

```python
from modelscope.msdatasets import MsDataset

manifest = MsDataset.load(
    "deidentified-head-dicom-sample-10",
    namespace="SHPDataGR",
    split="train",
)

for row in manifest:
    print(row["case_id"], row["archive_path"], row["archive_internal_path"], row["modality"])
```

下载病例 zip 后，可使用 pydicom、SimpleITK、MONAI、ITK-SNAP、3D Slicer 等工具读取 zip 内的 `case_xxx/dicom/**/*.dcm`。

## 使用说明

本数据集为医院真实影像病例数据经脱敏加工后的样例数据，仅用于科研、教学、算法测试和数据产品验证，不构成诊疗建议，也不应用于直接临床决策。使用者应结合具体场景复核 DICOM 头信息、影像质量和病案首页字段，并遵守平台协议和相关法律法规。

## 商务合作

如需更完整的医学影像数据、影像报告、结构化病案首页、电子病历、药品说明书、医学考试题库等数据资源，欢迎联系：

- 邮箱：zhouhaoran@shujuyoupu.com

README.md - 项目说明和使用指南 (3-Playbook 版本)
=================================================================

这个 Ansible 项目使用三个独立的 Playbook，在 vSphere 环境中分阶段、
参数化地创建 RHEL9 虚拟机。

-----
目录结构:
-----
.
├── playbook_1_create_batch1.yml   # 剧本1: 创建第一批虚拟机
├── playbook_2_create_batch2.yml   # 剧本2: 创建第二批虚拟机
├── playbook_3_attach_disk.yml     # 剧本3: 为第二批附加硬盘
├── requirements.yml               # Ansible 集合依赖
└── vars/
    └── main.yml                   # 所有可配置的变量 (共享)

-----
准备工作: 设置环境变量
-----

在运行 Playbook 之前，请先设置好以下环境变量：

```bash
export VCENTER_HOSTNAME="vcsnsx-vc.xxxxxx.com"
export VCENTER_USERNAME="sandbox-xxxxxxx@demo"
export VCENTER_PASSWORD="YOUR_VCENTER_PASSWORD"
export VCENTER_DATACENTER="Your_Datacenter_Name"
export VCENTER_CLUSTER="Your_Cluster_Name"
export VCENTER_DATASTORE="Your_Datastore_Name"
```

-----
执行步骤:
-----

1. **创建文件**:
   - 创建一个项目文件夹，并按上述“目录结构”创建所有文件和文件夹。
   - 将下面对应的内容复制到这些文件中。

2. **安装依赖**:
   ansible-galaxy collection install -r requirements.yml

3. **按顺序执行每个 Playbook**:

   # >> 步骤 1: 创建第一批的1台虚拟机
   ansible-playbook playbook_1_create_helper.yml

   # >> 步骤 2: 创建第二批的3台虚拟机
   ansible-playbook playbook_2_create_ocp.yml

   # >> 步骤 3: 为第二批的虚拟机附加硬盘
   ansible-playbook playbook_3_attach_disk.yml

=================================================================
---
title: Python 修改ip地址
date: 2020-08-28 11:07:36
tags:
categories: [Python]
---



在windows中使用python修改ip地址，需要[WMI (Windows Management Instrumentation )模块](https://pypi.org/project/WMI/#downloads):

```bash
pip install WMI
```



设置指定的ip地址：

```python
import wmi

# Obtain network adaptors configurations
nic_configs = wmi.WMI().Win32_NetworkAdapterConfiguration(IPEnabled=True)

# First network adaptor
nic = nic_configs[0]

# IP address, subnetmask and gateway values should be unicode objects
ip = u'192.168.0.11'
subnetmask = u'255.255.255.0'
gateway = u'192.168.0.1'

# Set IP address, subnetmask and default gateway
# Note: EnableStatic() and SetGateways() methods require *lists* of values to be passed
nic.EnableStatic(IPAddress=[ip],SubnetMask=[subnetmask])
nic.SetGateways(DefaultIPGateway=[gateway])
```



修改为通过DHCP自动获取ip：

```python
import wmi

# Obtain network adaptors configurations
nic_configs = wmi.WMI().Win32_NetworkAdapterConfiguration(IPEnabled=True)

# First network adaptor
nic = nic_configs[0]

# Enable DHCP
nic.EnableDHCP()
```

需要注意的是，在实际使用过程中，需要检查 [EnableStatic()](http://msdn.microsoft.com/en-us/library/aa390383(v=VS.85).aspx), [SetGateways()](http://msdn.microsoft.com/en-us/library/aa393301(v=VS.85).aspx) and [EnableDHCP()](http://msdn.microsoft.com/en-us/library/aa390378(v=VS.85).aspx) 的返回值，'0' 代表成功, '1' 代表需要重启，其它值需要查看上面链接的说明。返回值的格式如下：

```
(0,)
```

即，为python中tuple格式。

之上的方法在win7  python3.8中测试通过。








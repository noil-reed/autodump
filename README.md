# autodump

A lightweight tool to help you save structured data history.  

## Quickstart

### 1. Install package
```console
pip install autodump
```
### 2. Create a `autodump.yaml` in the path of your python script
```yaml
# Currently only support mysql as data storage. Will support more in the future. 
# Like other databases, local file using YAML/JSON etc.
medium: mysql
param:
# Mysql configurations
  host: localhost
  port: 3306
  user: user
  password: password
  database: db1
  table: tb1
```
### 3. Insert it in your code

#### Original code
```python
print("arguments: %s") % args 
for epoch in range(n_epoch): 
    for i, data in enumerate(trainloader, 0):
        inputs, labels = data

        optimizer.zero_grad()

        outputs = net(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()

        print('[%d, %5d] loss: %.3f' %
              (epoch + 1, i + 1, loss.item()))

print('Finished Training')
```

#### Replacing log with autodump
```python
# You only need to make sure the data source in autodump.yaml is connectable
# Don't need to pre-initialize any table or column
from autodump import Autodumper
dumper = Autodumper("experiment")

dumper.persist("arguments", args)
for epoch in range(n_epoch): 
    for i, data in enumerate(trainloader, 0):
        inputs, labels = data

        optimizer.zero_grad()

        outputs = net(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()

        dumper.cache("epoch", epoch + 1)
        dumper.cache("i", i + 1)
        dumper.cache("loss", loss.item())
        dumper.flush()

print('Finished Training')
```

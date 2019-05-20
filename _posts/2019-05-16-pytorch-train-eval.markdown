---
layout: post
comments: true
title:  "A note on using Drop out and Batch norm in Pytorch"
excerpt: ""
date:   2019-05-16 11:00:00
mathjax: true
---
 
One day, you notice that your deep learning model performs very poorly real data although it was very good in 
validation stage. A mistake that you may encounter is that your network might contain drop out and batch norm layers and
you handled them incorrectly. Below is an example of training an image classifier to classify the CIFAR-10 dataset:

```python
# Adapted from https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html
import torch
import torchvision
import torchvision.transforms as transforms

transform = transforms.Compose(
    [transforms.ToTensor(),
     transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])

trainset = torchvision.datasets.CIFAR10(root='./data', train=True,
                                        download=True, transform=transform)
trainloader = torch.utils.data.DataLoader(trainset, batch_size=4,
                                          shuffle=True, num_workers=2)

testset = torchvision.datasets.CIFAR10(root='./data', train=False,
                                       download=True, transform=transform)
testloader = torch.utils.data.DataLoader(testset, batch_size=4,
                                         shuffle=False, num_workers=2)

classes = ('plane', 'car', 'bird', 'cat',
           'deer', 'dog', 'frog', 'horse', 'ship', 'truck')
```

Define a CNN containing _Drop out_ and _Batch norm_ layers.

```python
import torch.nn as nn
import torch.nn.functional as F

class CNN(nn.Module):
    def __init__(self):
        super(CNN, self).__init__()
        # Convolution and pooling layers
        self.conv1 = nn.Conv2d(3, 6, 3)
        self.conv2 = nn.Conv2d(6, 16, 3)
        self.pool = nn.MaxPool2d(2, 2)
        # Dropout and batch norm layers
        self.dropout = nn.Dropout2d()
        self.batchnorm1 = nn.BatchNorm2d(6)
        self.batchnorm2 = nn.BatchNorm2d(16)
        # Fully connected layers
        self.fc1 = nn.Linear(16 * 9 * 36, 120)
        self.fc2 = nn.Linear(120, 10)
        
    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.dropout(x)
        x = self.batchnorm1(x)
        
        x = self.pool(F.relu(self.conv2(x)))
        x = self.dropout(x)
        x = self.batchnorm2(x)
        
        x = F.relu(self.fc1(x))
        x = self.fc2(x)
        return x
```

Define training and validation functions and start training:

```python
import torch.optim as optim

net = CNN()
optimizer = optim.Adam(net.parameters(), lr=3e-4)
criterion = nn.CrossEntropyLoss()
train_losses = []
val_losses = []
accuracies = []

def train(epoch):
    for i, data in enumerate(trainloader):
        inputs, targets = data
        optimizer.zero_grad()
        outputs = net(inputs)
        loss = criterion(outputs, targets)
        loss.backward()
        optimizer.step()
        
        if i % 1000 == 0:
            print('Train Epoch: {}\t Batch: {}\t \tLoss: {:.6f}'.format(
                epoch, i, loss.item()))
        train_losses.append(loss.item())
        torch.save(net.state_dict(), './models/model.pth')
        torch.save(optimizer.state_dict(), './ck_pts/optimizer.pth')

def validate(epoch):
    accuracy = 0.0
    with torch.no_grad():
        for i, data in enumerate(testloader):
            inputs, targets = data
            outputs = net(inputs)
            loss = criterion(outputs, targets)
            preds = torch.max(outputs, 1)[1]
            accuracy += (preds == targets).sum().item()
        val_losses.append(loss.item())
        print('Validation at epoch: {} \tLoss: {:.6f}\t Accuracy: {:.2f}%'.format(
            epoch, loss.item(), 100. * accuracy/len(testset)))
        accuracies.append(accuracy/len(testset))
        
# Train on 30 epochs
validate(0)
for epoch in range(10):
    train(epoch)
    validate(epoch)
```

After training, let's plot the loss curve as well as accuracy curve on the training and test set.
```python
fig = plt.figure()
plt.plot(train_counter, train_losses, color='blue')
plt.scatter(test_counter, test_losses, color='red')
plt.legend(['Train Loss', 'Test Loss'], loc='upper right')
plt.xlabel('number of training examples seen')
plt.ylabel('negative log likelihood loss')
fig

fig = plt.figure()
plt.plot(train_counter, train_losses, color='blue')
plt.scatter(test_counter, test_losses, color='red')
plt.legend(['Train Loss', 'Test Loss'], loc='upper right')
plt.xlabel('number of training examples seen')
plt.ylabel('negative log likelihood loss')
fig
```

# ARTS-44

## Algorithm

```java
package org.nocoder.codeabbey;

import java.util.Scanner;

/**
 * https://www.codeabbey.com/index/task_view/sums-in-loop
 */
public class SumsInLoop {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int total = scanner.nextInt();
        scanner.nextLine();
        System.out.println();

        String[] arr = new String[total];
        for (int i = 0; i < total; i++) {
            arr[i] = scanner.nextLine();
        }

        for (int i = 0; i < arr.length; i++) {
            String[] nums = arr[i].split(" ");
            int sum = 0;
            for (int j = 0; j < nums.length; j++) {
                int intValue = Integer.parseInt(nums[j]);
                sum += intValue;
            }
            System.out.print(sum + " ");
        }

        scanner.close();
    }
}
```

## Review

**React Components**

Components 像方法一样返回HTML元素。

Components 是独立的、可重用的。它们的作用与js function 类似，但是独立工作并返回 HTML。

Components 有两种类型，Class Components 和 Function Compoments。

> 在较老的React中，你可能会发现主要使用的是Class组建。现在建议使用Function组件和Hooks，它们是在React16.8中添加的。


React组件的名称必须以大写字母开头。

### Class Component

类组件必须包含extends React.Component 语句。这个语句创建一个React.Component的继承者，可以访问React.Component的方法。

组件还需要一个render()方法，该方法返回HTML。

创建一个名为Car的类组件：

```javascript

class Car extends React.Component {

    render(){
        return <h2>Hi, I am a Car!</h2>;
    }
}
```

### Function Component

Function 组件也返回HTML，它的行为与类组件非常类似，但是Function组件可以用更少的代码实现，更容易理解。

创建一个名为Car的Function组件

```javascript
function Car(){
    return return <h2>Hi, I am a Car!</h2>;
}
```

### Rendering a Component

现在有一个名为Car的组件，返回一个<h2>元素。

在程序中使用这个组件爱你，语法类似HTML:`<Car/>`

```javascript
ReactDOM.render(<Car/>, document.getElementById('root'));
```

### Props

组件可以作为`props`传递，代表properties。

Props 类似于方法参数，可以将它们作为属性传递到组件中。

```javascript
function Car(props){
    return <h2>I am a {props.color} Car!</h2>
}

ReactDOM.render(<Car color="red"/>, document.getElementById('root'))
```

### Components in Components

我们可以引用其他组件中的组件

使用Garage组件中的Car组件
```javascript
function Car(){
    return <h2>I am a Car!</h2>
}

// 车库
function Garage(){
    return (
        <>
            <h1>Who lives in my Garage?</h1>
            <Car />
        </>
    );
}

ReactDOM.render(<Garage/>, document.getElementById('root'));
```

### Components in Files

React 都是关于重用代码，并且建议将组件拆分到单独的文件中。

为此，创建一个扩展名为js的新文件，并将代码放进去。


创建一个Car.js文件

```javascript
function Car(){
    return <h2>Hi , I am a Car!</h2>
}

export default Car;
```

为了能够使用Car组件，必须在程序import这个文件。

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import Car from './Car.js';

ReactDOM.render(<Car/>, document.getElementById('root'));
```


## Tip

默认情况下，用`sudo dnf update` 或 `sudo yum update`后，会将Linux内核一起更新，过一段时间，grub2 就会生成好几个启动项，如果更新内核后使用系统没有问题，就可以把旧的内核删除。

我遇到的问题是，每次更新内核后，都需要重新安装显卡驱动，不然进不了GUI，这个很烦人，浪费时间，搞得我每次都有冲动重装系统。今天更新到5.15.12，又进不了桌面了，还好grub2可以让我选择从5.15.11启动。

> TMD，装win算了，至少没这么多麻烦...然后我就去搜win10镜像了...
> 行了吧你，Windows用不了几天你必然折腾回Linux，谁叫你爱折腾呢...
> 好吧，还是不要逃避问题...

冷静了一会儿，网上搜索试验了一番，把不用的内核都删除，然后dnf更新时排除内核，即不升级内核。

使用`uname -r` 查看当前使用的内核

```shell
[yangjl@fedora ~]$ uname -r
5.15.11-200.fc35.x86_64
```

使用`rpm -aq|grep kernel`查看已经安装的内核

```shell
[yangjl@fedora ~]$ sudo rpm -aq | grep kernel
libreport-plugin-kerneloops-2.15.2-6.fc35.x86_64
abrt-addon-kerneloops-2.14.6-9.fc35.x86_64
kernel-headers-5.15.4-200.fc35.x86_64
kernel-srpm-macros-1.0-6.fc35.noarch
kernel-core-5.15.11-200.fc35.x86_64
kernel-modules-5.15.11-200.fc35.x86_64
kernel-5.15.11-200.fc35.x86_64
kernel-modules-extra-5.15.11-200.fc35.x86_64
kernel-devel-5.15.11-200.fc35.x86_64
kernel-devel-5.15.12-200.fc35.x86_64
```

保留当前使用的内核，删除不用的内核 `sudo dnf remove -y kernel-core-5.15.12*`


`dnf` 和 `yum` 更新时可以使用exclude命令排除你不希望更新的package，例如更新除了firefox以外的所有package，可以这样

```shell
sudo dnf update --exclude=firefox
sudo yum update --exclude=firefox
```

再比如更新除了内核以外的所有package：

```shell
sudo dnf update --exclude=kernel*
sudo yum update --exclude=kernel*
```

如果觉得麻烦，在fedora下，可以通过修改`/etc/dnf/dnf.conf`，在`[main]`下添加`exclude=kernel*`,这样在使用`dnf update`和`yum update`时会排除kernel package 的更新。

/etc/dnf/dnf.conf

```shell
[main]
gpgcheck=1
installonly_limit=3
clean_requirements_on_remove=True
best=False
skip_if_unavailable=True
exclude=kernel*
```

参考文献

https://chewett.co.uk/blog/800/exclude-fedora-packages-updating-dnf-yum/
https://laplacence.github.io/2017/02/27/Fedora_delete_redundancy_kernel/

## Share
我们已经研究了Python语言的众多内容，现在我们将来学习一下怎么把这些内容结合起来。我们将设计编写一个能够 做 一些确实有用的事情的程序。

## 问题
我提出的问题是： 我想要一个可以为我的所有重要文件创建备份的程序。

尽管这是一个简单的问题，但是问题本身并没有给我们足够的信息来解决它。进一步的分析是必需的。例如，我们如何确定该备份哪些文件？备份保存在哪里？我们怎么样存储备份？

在恰当地分析了这个问题之后，我们开始设计我们的程序。我们列了一张表，表示我们的程序应该如何工作。对于这个问题，我已经创建了下面这个列表以说明 我 如何让它工作。如果是你设计的话，你可能不会这样来解决问题——每个人都有其做事的方法，这很正常。

1. 需要备份的文件和目录由一个列表指定。
2. 备份应该保存在主备份目录中。
3. 文件备份成一个zip文件。
4. zip存档的名称是当前的日期和时间。
5. 我们使用标准的zip命令，它通常默认地随Linux/Unix发行版提供。Windows用户可以使用Info-Zip程序。注意你可以使用任何地存档命令，只要它有命令行界面就可以了，那样的话我们可以从我们的脚本中传递参数给它。

## 解决方案
当我们基本完成程序的设计，我们就可以编写代码了，它是对我们的解决方案的实施。

### 版本一
**例10.1 备份脚本——版本一** 

```python
#!/usr/bin/python
# Filename: backup_ver1.py

import os
import time

# 1. The files and directories to be backed up are specified in a list.
source = ['/home/swaroop/byte', '/home/swaroop/bin']
# If you are using Windows, use source = [r'C:\Documents', r'D:\Work'] or something like that

# 2. The backup must be stored in a main backup directory
target_dir = '/mnt/e/backup/' # Remember to change this to what you will be using

# 3. The files are backed up into a zip file.
# 4. The name of the zip archive is the current date and time
target = target_dir + time.strftime('%Y%m%d%H%M%S') + '.zip'

# 5. We use the zip command (in Unix/Linux) to put the files in a zip archive
zip_command = "zip -qr '%s' %s" % (target, ' '.join(source))

# Run the backup
if os.system(zip_command) == 0:
    print 'Successful backup to', target
else:
    print 'Backup FAILED'
```

（源文件：[code/backup_ver1.py](http://woodpecker.org.cn/abyteofpython_cn/chinese/code/backup_ver1.py)）

### 输出

```
$ python backup_ver1.py
Successful backup to /mnt/e/backup/20041208073244.zip
```

现在，我们已经处于测试环节了，在这个环节，我们测试我们的程序是否正确工作。如果它与我们所期望的不一样，我们就得调试我们的程序，即消除程序中的 瑕疵 （错误）。

### 它如何工作
接下来你将看到我们如何把 设计 一步一步地转换为 代码 。

我们使用了os和time模块，所以我们输入它们。然后，我们在source列表中指定需要备份的文件和目录。目标目录是我们想要存储备份文件的地方，它由target_dir变量指定。zip归档的名称是目前的日期和时间，我们使用time.strftime()函数获得。它还包括.zip扩展名，将被保存在target_dir目录中。

time.strftime()函数需要我们在上面的程序中使用的那种定制。%Y会被无世纪的年份所替代。%m会被01到12之间的一个十进制月份数替代，其他依次类推。这些定制的详细情况可以在《Python参考手册》中获得。《Python参考手册》包含在你的Python发行版中。注意这些定制与用于print语句的定制（%后跟一个元组）类似（但不完全相同）

我们使用加法操作符来 级连 字符串，即把两个字符串连接在一起返回一个新的字符串。通过这种方式，我们创建了目标zip文件的名称。接着我们创建了zip_command字符串，它包含我们将要执行的命令。你可以在shell（Linux终端或者DOS提示符）中运行它，以检验它是否工作。

zip命令有一些选项和参数。-q选项用来表示zip命令安静地工作。-r选项表示zip命令对目录递归地工作，即它包括子目录以及子目录中的文件。两个选项可以组合成缩写形式-qr。选项后面跟着待创建的zip归档的名称，然后再是待备份的文件和目录列表。我们使用已经学习过的字符串join方法把source列表转换为字符串。

最后，我们使用os.system函数 运行 命令，利用这个函数就好像在 系统 中运行命令一样。即在shell中运行命令——如果命令成功运行，它返回0，否则它返回错误号。

根据命令的输出，我们打印对应的消息，显示备份是否创建成功。好了，就是这样我们已经创建了一个脚本来对我们的重要文件做备份！

> **给Windows用户的注释** 

> 你可以把source列表和target目录设置成任何文件和目录名，但是在Windows中你得小心一些。问题是Windows把反斜杠（\）作为目录分隔符，而Python用反斜杠表示转义符！
所以，你得使用转义符来表示反斜杠本身或者使用自然字符串。例如，使用'C:\\Documents'或r'C:\Documents'而不是'C:\Documents'——你在使用一个不知名的转义符\D！

现在我们已经有了一个可以工作的备份脚本，我们可以在任何我们想要建立文件备份的时候使用它。建议Linux/Unix用户使用前面介绍的可执行的方法，这样就可以在任何地方任何时候运行备份脚本了。这被称为软件的实施环节或开发环节。

上面的程序可以正确工作，但是（通常）第一个程序并不是与你所期望的完全一样。例如，可能有些问题你没有设计恰当，又或者你在输入代码的时候发生了一点错误，等等。正常情况下，你应该回到设计环节或者调试程序。

### 版本二
第一个版本的脚本可以工作。然而，我们可以对它做些优化以便让它在我们的日常工作中变得更好。这称为软件的维护环节。

我认为优化之一是采用更好的文件名机制——使用 时间 作为文件名，而当前的 日期 作为目录名，存放在主备份目录中。这样做的一个优势是你的备份会以等级结构存储，因此它就更加容易管理了。另外一个优势是文件名的长度也可以变短。还有一个优势是采用各自独立的文件夹可以帮助你方便地检验你是否在每一天创建了备份，因为只有在你创建了备份，才会出现那天的目录。

**例10.2 备份脚本——版本二** 

```python
#!/usr/bin/python
# Filename: backup_ver2.py

import os
import time

# 1. The files and directories to be backed up are specified in a list.
source = ['/home/swaroop/byte', '/home/swaroop/bin']
# If you are using Windows, use source = [r'C:\Documents', r'D:\Work'] or something like that

# 2. The backup must be stored in a main backup directory
target_dir = '/mnt/e/backup/' # Remember to change this to what you will be using

# 3. The files are backed up into a zip file.
# 4. The current day is the name of the subdirectory in the main directory
today = target_dir + time.strftime('%Y%m%d')
# The current time is the name of the zip archive
now = time.strftime('%H%M%S')

# Create the subdirectory if it isn't already there
if not os.path.exists(today):
    os.mkdir(today) # make directory
    print 'Successfully created directory', today

# The name of the zip file
target = today + os.sep + now + '.zip'

# 5. We use the zip command (in Unix/Linux) to put the files in a zip archive
zip_command = "zip -qr '%s' %s" % (target, ' '.join(source))

# Run the backup
if os.system(zip_command) == 0:
    print 'Successful backup to', target
else:
    print 'Backup FAILED'
```

（源文件：[code/backup_ver2.py](http://woodpecker.org.cn/abyteofpython_cn/chinese/code/backup_ver2.py)）

### 输出

```
$ python backup_ver2.py
Successfully created directory /mnt/e/backup/20041208
Successful backup to /mnt/e/backup/20041208/080020.zip

$ python backup_ver2.py
Successful backup to /mnt/e/backup/20041208/080428.zip
```

### 它如何工作
两个程序的大部分是相同的。改变的部分主要是使用os.exists函数检验在主备份目录中是否有以当前日期作为名称的目录。如果没有，我们使用os.mkdir函数创建。

注意os.sep变量的用法——这会根据你的操作系统给出目录分隔符，即在Linux、Unix下它是'/'，在Windows下它是'\\'，而在Mac OS下它是':'。使用os.sep而非直接使用字符，会使我们的程序具有移植性，可以在上述这些系统下工作。

### 版本三
第二个版本在我做较多备份的时候还工作得不错，但是如果有极多备份的时候，我发现要区分每个备份是干什么的，会变得十分困难！例如，我可能对程序或者演讲稿做了一些重要的改变，于是我想要把这些改变与zip归档的名称联系起来。这可以通过在zip归档名上附带一个用户提供的注释来方便地实现。

**例10.3 备份脚本——版本三（不工作！）** 

```python
#!/usr/bin/python
# Filename: backup_ver3.py

import os
import time

# 1. The files and directories to be backed up are specified in a list.
source = ['/home/swaroop/byte', '/home/swaroop/bin']
# If you are using Windows, use source = [r'C:\Documents', r'D:\Work'] or something like that

# 2. The backup must be stored in a main backup directory
target_dir = '/mnt/e/backup/' # Remember to change this to what you will be using

# 3. The files are backed up into a zip file.
# 4. The current day is the name of the subdirectory in the main directory
today = target_dir + time.strftime('%Y%m%d')
# The current time is the name of the zip archive
now = time.strftime('%H%M%S')

# Take a comment from the user to create the name of the zip file
comment = raw_input('Enter a comment --> ')
if len(comment) == 0: # check if a comment was entered
    target = today + os.sep + now + '.zip'
else:
    target = today + os.sep + now + '_' +
        comment.replace(' ', '_') + '.zip'

# Create the subdirectory if it isn't already there
if not os.path.exists(today):
    os.mkdir(today) # make directory
    print 'Successfully created directory', today

# 5. We use the zip command (in Unix/Linux) to put the files in a zip archive
zip_command = "zip -qr '%s' %s" % (target, ' '.join(source))

# Run the backup
if os.system(zip_command) == 0:
    print 'Successful backup to', target
else:
    print 'Backup FAILED'    
```

（源文件：[code/backup_ver3.py](http://woodpecker.org.cn/abyteofpython_cn/chinese/code/backup_ver3.py)）

### 输出

```
$ python backup_ver3.py
File "backup_ver3.py", line 25
target = today + os.sep + now + '_' +
                                ^
SyntaxError: invalid syntax
```

### 它如何（不）工作
这个程序不工作！Python说有一个语法错误，这意味着脚本不满足Python可以识别的结构。当我们观察Python给出的错误的时候，它也告诉了我们它检测出错误的位置。所以我们从那行开始 调试 我们的程序。

通过仔细的观察，我们发现一个逻辑行被分成了两个物理行，但是我们并没有指明这两个物理行属于同一逻辑行。基本上，Python发现加法操作符（＋）在那一逻辑行没有任何操作数，因此它不知道该如何继续。记住我们可以使用物理行尾的反斜杠来表示逻辑行在下一物理行继续。所以，我们修正了程序。这被称为修订。

### 版本四

**例10.4 备份脚本——版本四**

 ```python
 #!/usr/bin/python
# Filename: backup_ver4.py

import os
import time

# 1. The files and directories to be backed up are specified in a list.
source = ['/home/swaroop/byte', '/home/swaroop/bin']
# If you are using Windows, use source = [r'C:\Documents', r'D:\Work'] or something like that

# 2. The backup must be stored in a main backup directory
target_dir = '/mnt/e/backup/' # Remember to change this to what you will be using

# 3. The files are backed up into a zip file.
# 4. The current day is the name of the subdirectory in the main directory
today = target_dir + time.strftime('%Y%m%d')
# The current time is the name of the zip archive
now = time.strftime('%H%M%S')

# Take a comment from the user to create the name of the zip file
comment = raw_input('Enter a comment --> ')
if len(comment) == 0: # check if a comment was entered
    target = today + os.sep + now + '.zip'
else:
    target = today + os.sep + now + '_' + \
        comment.replace(' ', '_') + '.zip'
    # Notice the backslash!

# Create the subdirectory if it isn't already there
if not os.path.exists(today):
    os.mkdir(today) # make directory
    print 'Successfully created directory', today

# 5. We use the zip command (in Unix/Linux) to put the files in a zip archive
zip_command = "zip -qr '%s' %s" % (target, ' '.join(source))

# Run the backup
if os.system(zip_command) == 0:
    print 'Successful backup to', target
else:
    print 'Backup FAILED'
 ```
 
（源文件：[code/backup_ver4.py](http://woodpecker.org.cn/abyteofpython_cn/chinese/code/backup_ver4.py)）

### 输出

```
$ python backup_ver4.py
Enter a comment --> added new examples
Successful backup to /mnt/e/backup/20041208/082156_added_new_examples.zip

$ python backup_ver4.py
Enter a comment -->
Successful backup to /mnt/e/backup/20041208/082316.zip
```

### 它如何工作
这个程序现在工作了！让我们看一下版本三中作出的实质性改进。我们使用raw_input函数得到用户的注释，然后通过len函数找出输入的长度以检验用户是否确实输入了什么东西。如果用户只是按了回车（比如这只是一个惯例备份，没有做什么特别的修改），那么我们就如之前那样继续操作。

然而，如果提供了注释，那么它会被附加到zip归档名，就在.zip扩展名之前。注意我们把注释中的空格替换成下划线——这是因为处理这样的文件名要容易得多。

### 进一步优化
对于大多数用户来说，第四个版本是一个满意的工作脚本了，但是它仍然有进一步改进的空间。比如，你可以在程序中包含 交互 程度——你可以用-v选项来使你的程序更具交互性。

另一个可能的改进是使文件和目录能够通过命令行直接传递给脚本。我们可以通过sys.argv列表来获取它们，然后我们可以使用list类提供的extend方法把它们加到source列表中去。

我还希望有的一个优化是使用tar命令替代zip命令。这样做的一个优势是在你结合使用tar和gzip命令的时候，备份会更快更小。如果你想要在Windows中使用这些归档，WinZip也能方便地处理这些.tar.gz文件。tar命令在大多数Linux/Unix系统中都是默认可用的。Windows用户也可以下载安装它。

命令字符串现在将称为：

```python
tar = 'tar -cvzf %s %s -X /home/swaroop/excludes.txt' % (target, ' '.join(srcdir))
```

选项解释如下：

- -c表示创建一个归档。

- -v表示交互，即命令更具交互性。

- -z表示使用gzip滤波器。

- -f表示强迫创建归档，即如果已经有一个同名文件，它会被替换。

- -X表示含在指定文件名列表中的文件会被排除在备份之外。例如，你可以在文件中指定*~，从而不让备份包括所有以~结尾的文件。

> **重要**

> 最理想的创建这些归档的方法是分别使用zipfile和tarfile。它们是Python标准库的一部分，可以供你使用。使用这些库就避免了使用os.system这个不推荐使用的函数，它容易引发严重的错误。
然而，我在本节中使用os.system的方法来创建备份，这纯粹是为了教学的需要。这样的话，例子就可以简单到让每个人都能够理解，同时也已经足够用了。

## 软件开发过程
现在，我们已经走过了编写一个软件的各个环节。这些环节可以概括如下：

1. 什么（分析）
2. 如何（设计）
3. 编写（实施）
4. 测试（测试与调试）
5. 使用（实施或开发）
6. 维护（优化）

> **重要**

> 我们创建这个备份脚本的过程是编写程序的推荐方法——进行分析与设计。开始时实施一个简单的版本。对它进行测试与调试。使用它以确信它如预期那样地工作。再增加任何你想要的特性，根据需要一次次重复这个编写－测试－使用的周期。记住“软件是长出来的，而不是建造的”。

## 概括
我们已经学习如何创建我们自己的Python程序/脚本，以及在编写这个程序中所设计到的不同的状态。你可以发现它们在创建你自己的程序的时候会十分有用，让你对Python以及解决问题都变得更加得心应手。

接下来，我们将讨论面向对象的编程。
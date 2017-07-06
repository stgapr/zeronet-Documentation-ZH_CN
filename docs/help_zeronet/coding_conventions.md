# 假如你要参与ZeroNet的开发工作，请使用以下编程风格
 - [PEP8](https://www.python.org/dev/peps/pep-0008/)
 - 简洁的代码
 - 过早优化是万恶之源

### 命名
 - 类命名: 首字母大写的驼峰命名法
 - 函数命名: 首字母小写的驼峰命名法
 - 变量命名: 全部小写，单词间使用下划线

### 变量
 - file_path: 通过程序运行路径来引用的文件 (data/17ib6teRqdVgjB698T4cD1zDXKgPqpkrMg/css/all.css)
 - inner_path: 通过站点相对路径来引用的文件 (css/all.css)
 - file_name: all.css
 - file: Python文件对象
 - privatekey: 站点的私钥 (without _)

### 源文件、目录和命名
 - 每个文件最好只有一个类
 - 源文件名和目录名最好用以类的名称来命名，例如：WorkerManager class = Worker/WorkerManager.py

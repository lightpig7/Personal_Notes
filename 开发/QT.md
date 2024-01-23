### 注意的点：

给text框添加默认值的placeholder中实现换行：

```
在图形化界面的placeholder中不会解析\n，得在mainwindow.cpp里面进行改写

ui->textEdit_6->setPlaceholderText("0 1 4\n0 4 1\n0 5 2\n1 2 6\n1 5 3\n2 3 6\n2 5 5\n3 4 4\n3 5 5\n4 5 3");
```



这里不加std::cout<<runKruskal(m, n, edges);点击按钮后程序会崩溃，我也不知道为什么

```
    QString result;
    result += QString::fromStdString(runPrim(m, n, edges));
    std::cout<<runKruskal(m, n, edges);
    ui->textEdit_8->append(result);
```

# Eclipse快捷键及代码规范

------

**自定义快捷键:**

Windows--Keys--选择功能--Binding设置--apply

例：

Ctrl+shift+p  创建包

Ctrl+shift+n  创建类



创建类时勾选Generate comments 默认注释

**默认注释：**

Windows--Perferences--搜索temp--Java-Code Style-Code Templates--Comments-Files--Edit修改版权信息

--Types修改作者和项目说明



**设置默认编码格式：**

Windows--Perferences--搜索wor--General--下面Workspace--选择UTF-8



**设置提醒：**

Windows--Perferences--搜索assi--Java--Editor--Content Assist--Auto Activation 第二个.+26个小写字母+26个大写字母



**快捷键**

1.ctrl+/                       单行注释

2.ctrl+shift+/             多行注释 

   ctrl+shift+\             取消多行注释

3.ctrl+shift+f             格式化代码    ???

4.ctrl+d                      删除代码（一行）多行需要选中

5.ctrl+q                      跳到最后一次写代码的位置

6.ctrl+alt+方向键↑/↓  向上/下复制代码

7.ctrl+L                      快速定位行

8.alt+方向键↑/↓          上下行交换内容或把当前行内容把上或下移动

9.alt+shift+a              捕获异常     

10.ctrl+m                   最大化还原编辑窗口   

11.ctrl+w                   关闭当前编辑窗口

12.ctrl+shift+w          关闭所有编辑窗口

13.ctrl+shift+o          导包

14.ctrl+1                    代码建议

15.ctrl+e                    显示文件列表，快速转换编辑器

16.shift+Enter            直接跳到下一行

17.shift+alt+s            生成构造方法

18.“ /**“ +Enter     添加注释

例： /**

​         \* 添加部门

​         \* @param dept  部门对象

​         \* @return  添加成功返回true,否则返回false

​         */

19.alt+? 或 alt+/：自动补全代码或者提示代码

20.ctrl+o：快速outline视图

21.ctrl+shift+r：打开资源列表



**添加阿里巴巴代码规范插件**

代码质量检查  PMD  FindBugs  Sonar



**生成javadoc文档：**

Project--Generate Javadoc--展开项目--选择项目的所有包--Next--Next--填写-encoding utf-8 -charset utf-8 -Finish --Yes to all   

选择项目--doc--show in--System Exploer--index.html

**文档能力很重要**

**生成移动webapp：**

使用HBuilder，选中项目，右键选择转成移动App，生成json文件，双击json文件，云端获取id，下边可选择图标配置（png），启动图片配置(png)，Ctrl+S保存。选中工程，发行-云打包-打原生安装包，可取消广告。打包后下载文件。
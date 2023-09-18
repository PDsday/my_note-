		CheckBox可以创建一个勾选框，勾选框能够设定出勾选状态和非勾选两种，变化窗台可以触发事件。

### 像窗体拖入勾选框

		向空白工程中添加一个勾选框。
		1、大小变化
		在没有容器的限制下，AutoSize改为False可以自由定义控件的大小。

![[Pasted image 20230901150909.png]]
![[Pasted image 20230901150916.png]]

		但是不会改变勾选框的大小。
		2、文字变化
		文字大小格式，居中方式都是常见设置。修改本文位置：

![[Pasted image 20230901151110.png]]

		修改字体位置：

![[Pasted image 20230901151128.png]]

		3、为该控件添加图片也是可以的，也不会遮挡勾选框。

![[Pasted image 20230901151401.png]]

		4、修改勾选框的左右位置可以设置这个属性，RightToLeft

![[Pasted image 20230901151708.png]]

			选择Yes，勾选框的位置会移动到左边。这其实是文本/图片左对齐还是右对齐，但勾选框会跟着变化，可以留意一下。

![[Pasted image 20230901151753.png]]

		真正调节勾选框位置的属性，应该是CheckAlign

![[Pasted image 20230901151943.png]]

		通过这个属性就可以自定义勾选框所在的位置了。例如选择到右上角：设定勾选框的时候，注意RightToLeft的选择，否则会左右颠倒。

![[Pasted image 20230901152047.png]]

		4、默认勾选
		checked属性就可以设置是否默认勾选了

![[Pasted image 20230901152252.png]]
		
		选择true图片将启动时就会选上，这个属性也是编程时，获取或者读取勾选状态的成员。

![[Pasted image 20230901152355.png]]

		5、定义光标效果
		当用户的鼠标移动到这里，希望鼠标的箭头换成别惹效果，可以设置cursor属性，提供了相当丰富的箭头选择。

![[Pasted image 20230901152552.png]]

		6、三种选中状态
		checkbox可以选择三种选中状态，这样子checkbox就可以实现三种效果切换了。

![[Pasted image 20230901152804.png]]

		第三种选中的效果样式，是一个蓝点。

![[Pasted image 20230901152833.png]]

		7、如果不想设定成单独一个框体配上文本，而是将框体和文本结合，使得勾选控件编程和按键一样的效果，可以选择外观Appearance。

![[Pasted image 20230901154327.png]]


### 代码

		checkbox勾选状态变化时，可以产生事件，可以如下编程：

```C#
 private void checkBox1_CheckedChanged(object sender, EventArgs e)
 {
     if (((CheckBox)sender).Checked == true)
     {
         MessageBox.Show("勾选");
         //也可以手动更改勾选的状态
         ((CheckBox)sender).CheckState = CheckState.Unchecked;
     }
     else
     {
         MessageBox.Show("取消勾选");
         ((CheckBox)sender).CheckState = CheckState.Checked;
     }
 }

//感受永远逃不出的勾选事件循环把！！乔鲁诺·乔巴纳

```

![[Pasted image 20230901155536.png]]


### CheckBoxList

		如果需要又多个勾选时，可以使用checkboxlist=，它右上角的小黑点可以自由的编辑勾选框的数量，每个行一个字符串可以创建一个勾选框，字符串会编程勾选框的描述文字。

![[Pasted image 20230901155925.png]]

		这种勾选框产生的勾选事件，都是统一的，因此需要程序对具体的勾选框做出区分。反正这个控件就是不太好用，在一些简单场合，CheckBoxList要更方便。

```C#

private void checkedListBox1_SelectedIndexChanged(object sender, EventArgs e)
 {

     int num = this.checkedListBox1.Items.Count;

     for (int i = 0; i < num; i++)
     {
         if (((CheckedListBox)sender).GetItemChecked(i) == true)
         {
             MessageBox.Show(("选中了第" + i.ToString() + "项"));
         }
     }
     
 }

```

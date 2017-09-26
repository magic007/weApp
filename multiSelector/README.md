# 强大的多列选择器

>有些时候，三级联动业务场景并不只是全国地区选择，可能还涉及到自定义分类的三级联动，这时就需要使用微信的多列选择器。

----
>如果只是一列字段，或者每次拖动一次都去服务端取，会比较容易。 如果想一次定义好`json`,关联数据相对比较复杂，此案例`json`结构如下：


![WX20170926-150637@2x.png](http://upload-images.jianshu.io/upload_images/4276407-6e0a603d52c31256.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

-----
效果如下
![image.png](http://upload-images.jianshu.io/upload_images/4276407-db448d02ef96d1ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


实现过程

1.WXML(这是一段微信官方文档的代码)
```
<view class="section">
  <view class="section__title">普通选择器</view>
  <picker bindchange="bindPickerChange" value="{{index}}" range="{{array}}">
    <view class="picker">
      当前选择：{{array[index]}}
    </view>
  </picker>

    <picker bindchange="bindPickerChange1" value="{{index}}" range="{{array}}">
    <view class="picker">
      当前选择：{{array[index]}}
    </view>
  </picker>
</view>

```


2.js 逻辑部分
```

// 第一步  data 定义数据
ssl:地区json

//第二步  设置默认三级联动数据
Onload

//获取省市区json
var ssls = this.data.ssl;
//定义对应变量
    var sheng = [];
    var shi = [];
    var qu = [];
    for (var i in ssls) {
      sheng.push(ssls[i].p_name)
      if (i == 0) {
        for (var u in ssls[i].p_city) {
          // console.log(ssls[i].p_city[u].c_name)
          shi.push(ssls[i].p_city[u].c_name)
          for (var j in ssls[i].p_city[u].p_district) {
            // console.log(ssls[i].p_city[u].c_name)
            qu.push(ssls[i].p_city[u].p_district[j].d_name)
          }
        }
      }

    }
    console.log(sheng, shi);
    this.setData({
      multiArray: [sheng, shi, qu]
    });

//第三步：选择对应的value值改变
  bindMultiPickerChange: function (e) {
    console.log('picker发送选择改变，携带值为', e.detail.value)
    this.setData({
      multiIndex: e.detail.value
    })
  },

//第四步：下拉拖动选项事件（这里只需要处理第一栏，与第二栏【因为第三栏拖动的时候，没有第四栏关联变动了，关于此处讲的栏，请看代码后图1-3】）

bindMultiPickerColumnChange: function (e) {
    console.log('修改的列为', e.detail.column, '，值为', e.detail.value);
    var data = {
      multiArray: this.data.multiArray,
      multiIndex: this.data.multiIndex
    };
    data.multiIndex[e.detail.column] = e.detail.value;
    switch (e.detail.column) {
      case 0:
        this.setData({
          thisSheng: e.detail.value
        })
        //此处是拖动第一栏的时候处理
        var row = this.getCity(e.detail.value);
        data.multiArray[1] = row[0];
        data.multiArray[2] = row[1];

        //此处默认选中第一项
        data.multiIndex[1] = 0;
        data.multiIndex[2] = 0;
        break;
      case 1:
         //此处是拖动第二栏的时候处理
        var row = this.getCity(this.data.thisSheng, e.detail.value);
        console.log(row);
        data.multiArray[2] = row[1];
        data.multiIndex[2] = 0;
        console.log(data.multiIndex);
        break;
    }
    this.setData(data);
  },
```
---
![图1-2.png](http://upload-images.jianshu.io/upload_images/4276407-be01613c573a790d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

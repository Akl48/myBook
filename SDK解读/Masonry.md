# Masonry

## 代码案例

```objc
    [self.ImageView mas_makeConstraints:^(MASConstraintMaker *make){
        make.left.equalTo(self.view.mas_left).offset(15);
        make.right.equalTo(self.view.mas_right).offset(-15);
        make.centerY.equalTo(self.contentView.mas_centerY);
    }]
```

## 解析


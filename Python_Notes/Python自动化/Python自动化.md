# Python自动化

# 操作图像

pillow模块, image类

```python
from PIL import Image, ImageFilter

# 打开图像
image = Image.open('./res/guido.jpg')
# 图像基本信息
print(image.format, image.size, image.mode)
# 显示图像
image.show()

# 剪裁图像
rect = 80, 20, 310, 360
image.crop(rect).show()

# 生成缩略图
size = 128, 128
image.thumbnail(size)

# 缩放和粘贴图像
rect = 80, 20, 310, 360
guido_head = image.crop(rect)
width, height = guido_head.size
# paste方法把一参粘贴到对象中, 位置为二参
# resize方法参数为元组类型
image.paste(guido_head.resize((int(width/1.5), int(height/1.5))), (172, 40))

# 旋转
image.rotate(180).show()
# 翻转
image.transpose(Image.FLIP_LEFT_RIGHT).show()

# 操作像素
# putpixel方法, 在一参指定位置放置二参指定颜色的像素点
for x in range(80, 310):
	for y in range(20, 360):
		image.putpixel((x, y), (128, 128, 128))

# 滤镜效果
image.filter(ImageFilter.CONTOUR).show()
```

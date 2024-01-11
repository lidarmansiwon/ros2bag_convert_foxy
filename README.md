# Edit ros2bag_convert source code for using in foxy ros2

<<"message_conventer.py">>

```
def _is_ros_binary_type(field_type):
""" Checks if the field is a binary array one, fixed size or not
list(bytearray(de(encoded_data)))
_is_ros_binary_type("uint8")
>>> False
_is_ros_binary_type("uint8[]")
>>> True
_is_ros_binary_type("uint8[3]")
>>> True
_is_ros_binary_type("char")
>>> False
_is_ros_binary_type("char[]")
>>> True
_is_ros_binary_type("char[3]")
>>> True
_is_ros_binary_type("octet")
>>> True
"""
return field_type.startswith('uint8[') or field_type.startswith('char[') or **field_type.startswith('octet') **
```
----------------------------------------------------------------------------------------------------
```
def convert_ros_message_to_dictionary(message):
    """
    Takes in a ROS message and returns a Python dictionary.

    Example:
        ros_message = std_msgs.msg.String(data="Hello, Robot")
        dict_message = convert_ros_message_to_dictionary(ros_message)
    """

    **dictionary = {}
    if hasattr(message, 'get_fields_and_field_types'):
        message_fields = message.get_fields_and_field_types()
        for (field_name, field_type) in message_fields.items():
            field_value = getattr(message, field_name)
            dictionary[field_name] = _convert_from_ros_type(field_type, field_value)
    else:
        dictionary = None

    return dictionary **
```
----------------------------------------------------------------------------------------------------
<< "main.py" >>

    for i in range(len(data)):
        topic_name,topic_type = data[i][0],data[i][1]
        save_csv_file.save_csv_file(data[i][2:],file_url[:file_url.rfind("/")]+".csv")



# ros2bag_convert

[中文](README.md) | [English](README_EN.md)

**将ROS2的Bag文件转换为CSV、JSON等。**

## 一、安装

命令行安装：

```
sudo pip install ros2bag_convert
```

下载安装：

```
git clone git@github.com:fishros/ros2bag_convert.git
cd ros2_convert
python3 setup.py bdist_wheel
sudo pip install dist/ros2bag_convert-0.1.0-py3-none-any.whl
```

## 二、使用

目前仅支持将数据转换为csv格式，结果将输出到`xxx.db3`同级目录。

```
ros2bag-convert xxxx.db3
```

### 测试指令

#### 手动发布Pose数据

```
ros2 topic pub test geometry_msgs/msg/Pose  '{position:{x: 0.0,y: 0.0,z: 0.0}, orientation: {x: 0.0,y: 0.0,z: 0.0,w: 1.0}}'
```

#### 记录

```
ros2 bag record test
```

#### 转换

```
ros2bag-convert xxxx.db3
```

## 作者

- [小鱼-鱼香ROS](https://www.fishros.com)

## 版本记录

- 20210830-V0.1.0
  - 完成基础转换功能
  - 已知bug：数据未按层级展开输出

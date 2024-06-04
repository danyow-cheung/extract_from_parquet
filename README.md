# extract_from_parquet
using python to extract data from .parquet file 

## sample code 
```python
import pandas as pd 
from tqdm import tqdm 
from PIL import Image 
import os 
import io 
import json 
file_path  = "data/train-00000-of-00001-78e564002aa9c8f0.parquet"
data_path = "raw_data/"
raw_data_pd = pd.read_parquet(file_path)
print(raw_data_pd.head(5))


imgs_list = raw_data_pd['image']
englist_text_list = raw_data_pd['en_text']
chinese_text_list = raw_data_pd['zh_text']


def bytes_to_image(byte_data, file_path):
    """
    将字节流数据转换为图片并保存到指定文件路径。
    
    Args:
        byte_data (bytes): 包含图片数据的字节流。
        file_path (str): 保存图片的文件路径。
    """
    # 使用 io.BytesIO 将字节流转换为 image 对象
    image = Image.open(io.BytesIO(byte_data))    
    # 保存图片
    image.save(file_path)

print(type(englist_text_list),type(chinese_text_list))

def series2json(json_data,file_path):
    # 将 Pandas Series 转换为 JSON 格式
    json_data = json_data.to_json()

    # 将 JSON 数据保存到文件
    with open(file_path, 'w',encoding='utf-8') as f:
        f.write(json_data)

# eng text 
eng_save_path = series2json(englist_text_list,os.path.join(data_path,'english.json'))
# chi text 
chi_text_list = []
for i in chinese_text_list:
    print(i,type(i))
    chi_text_list.append(i)
    # break 
# 将 Python 列表转换为 JSON 格式
json_data = json.dumps(chi_text_list,ensure_ascii=False)

# 将 JSON 数据保存到文件
with open('data.json', 'w') as f:
    f.write(json_data)

# chi_save_path = series2json(chinese_text_list,os.path.join(data_path,'chinese.json'))
# extract from image 
for idx,img_bytes in tqdm(enumerate(imgs_list),desc='extracting images to file'):
    
     img_byte = img_bytes['bytes']
     save_path = os.path.join(data_path,f"{idx}.png")
     bytes_to_image(img_byte,save_path)



```

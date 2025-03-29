---

## **📝 Bài tập 4: Phân tích dữ liệu thời tiết**
#### **<span style="color: red;">Author:</span>** Nguyễn Tuấn Anh  
#### **<span style="color: red;">Class:</span>** K57KMT  
#### **<span style="color: red;">ID student:</span>** K215480106003  


---

📥 **Dữ liệu:** [Hourly Weather Data](https://www.kaggle.com/datasets/selfishgene/historical-hourly-weather-data)

### **Yêu cầu bài tập**
1. **Đọc dữ liệu và kiểm tra thông tin cột thời gian**.
2. **Lọc dữ liệu**: Chỉ giữ lại dữ liệu từ năm 2020 trở đi.
3. **Trích xuất thông tin thời gian**: Thêm cột `year`, `month`, `day_of_week`, `hour`.
4. **Lấy mẫu lại dữ liệu**: Chuyển từ dữ liệu theo giờ thành **trung bình nhiệt độ theo ngày**.
5. **Nội suy dữ liệu bị thiếu** để điền nhiệt độ bị khuyết.
6. **Tính toán xu hướng thời gian**: Tính nhiệt độ trung bình theo tháng và vẽ biểu đồ.

### **Ví dụ kết quả mong đợi**
Sau khi resample, dữ liệu có dạng:
| Date       | Avg_Temp |
|------------|---------|
| 2020-01-01 | 5.2     |
| 2020-01-02 | 4.8     |
| 2020-01-03 | 3.1     |

### **Gợi ý cách làm**
- Dùng `.to_datetime(df["datetime"])` để chuyển đổi cột thời gian.
- Lọc dữ liệu bằng điều kiện `df["datetime"].dt.year >= 2015`.
- Sử dụng `.resample("D", on="datetime").mean()` để lấy nhiệt độ trung bình theo ngày.
- Dùng `.interpolate()` để nội suy nhiệt độ bị thiếu.
- Vẽ biểu đồ xu hướng nhiệt độ theo tháng bằng Matplotlib.

---
```python
pip install kagglehub
```


```python
pip install pandas
```

    Collecting pandas
      Using cached pandas-2.2.3-cp310-cp310-win_amd64.whl (11.6 MB)
    Requirement already satisfied: pytz>=2020.1 in c:\users\ngtua\appdata\local\programs\python\python310\lib\site-packages (from pandas) (2025.2)
    Collecting numpy>=1.22.4
      Using cached numpy-2.2.4-cp310-cp310-win_amd64.whl (12.9 MB)
    Collecting tzdata>=2022.7
      Using cached tzdata-2025.2-py2.py3-none-any.whl (347 kB)
    Requirement already satisfied: python-dateutil>=2.8.2 in c:\users\ngtua\appdata\roaming\python\python310\site-packages (from pandas) (2.9.0.post0)
    Requirement already satisfied: six>=1.5 in c:\users\ngtua\appdata\roaming\python\python310\site-packages (from python-dateutil>=2.8.2->pandas) (1.17.0)
    Installing collected packages: tzdata, numpy, pandas
    Successfully installed numpy-2.2.4 pandas-2.2.3 tzdata-2025.2
    Note: you may need to restart the kernel to use updated packages.
    

      WARNING: The scripts f2py.exe and numpy-config.exe are installed in 'c:\Users\ngtua\AppData\Local\Programs\Python\Python310\Scripts' which is not on PATH.
      Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
    
    [notice] A new release of pip is available: 23.0.1 -> 25.0.1
    [notice] To update, run: python.exe -m pip install --upgrade pip
    


```python
import kagglehub

# Download latest version
path = kagglehub.dataset_download("selfishgene/historical-hourly-weather-data")

print("Path to dataset files:", path)
```

    c:\Users\ngtua\AppData\Local\Programs\Python\Python310\lib\site-packages\tqdm\auto.py:21: TqdmWarning: IProgress not found. Please update jupyter and ipywidgets. See https://ipywidgets.readthedocs.io/en/stable/user_install.html
      from .autonotebook import tqdm as notebook_tqdm
    

    Downloading from https://www.kaggle.com/api/v1/datasets/download/selfishgene/historical-hourly-weather-data?dataset_version_number=2...
    

    100%|██████████| 12.0M/12.0M [00:03<00:00, 3.76MB/s]

    Extracting files...
    

    
    

    Path to dataset files: C:\Users\ngtua\.cache\kagglehub\datasets\selfishgene\historical-hourly-weather-data\versions\2
    


```python
import pandas as pd

#1. Đọc dữ liệu từ các file csv
df1  = pd.read_csv("city_attributes.csv")
df2 = pd.read_csv("humidity.csv", parse_dates=['datetime'])
df3  = pd.read_csv("pressure.csv", parse_dates=['datetime'])
df4 = pd.read_csv("temperature.csv", parse_dates=['datetime'])
df5  = pd.read_csv("weather_description.csv", parse_dates=['datetime'])
df6  = pd.read_csv("wind_direction.csv", parse_dates=['datetime'])
df7  = pd.read_csv("wind_speed.csv", parse_dates=['datetime'])
#print(df2)
print(df2['datetime'].dtype)
#Kiểm tra thông tin cột thời gian df2, df3, df5, df6, df7 


```

    datetime64[ns]
    


```python
# Thông tin cơ bản của cột thời gian
print(df2['datetime'].min())  # Ngày nhỏ nhất
print(df2['datetime'].max())  # Ngày lớn nhất

# Kiểm tra các giá trị null
print(df2['datetime'].isnull().sum())  # Đếm giá trị null
```

    2012-10-01 12:00:00
    2017-11-30 00:00:00
    0
    


```python
# 2. Lọc dữ liệu, chỉ dữ lại từ năm 2015 trở đi
# Lọc dữ liệu từ năm 2015 trở đi
df_filtered = df2[df2['datetime'].dt.year >= 2015]

# Hiển thị dữ liệu đã lọc
print(df_filtered)

```


```python
# 3. Trích xuất thông tin thời gian
# Giả sử 'datetime' là cột thời gian đã được chuyển thành kiểu datetime
df2['nam'] = df2['datetime'].dt.year
df2['thang'] = df2['datetime'].dt.month
df2['thu'] = df2['datetime'].dt.day_name()  
df2['gio'] = df2['datetime'].dt.hour

print(df2.to_string())


```


```python
#xuất sang file excel để xem dữ liệu
df2.to_csv('df2.csv', index=False)

```


```python
# 4. **Lấy mẫu lại dữ liệu**: Chuyển từ dữ liệu theo giờ thành **trung bình nhiệt độ theo ngày**.
# Lấy mẫu lại dữ liệu, tính trung bình nhiệt độ theo ngày
df_daily = df4.resample('D', on='datetime').mean()

# Hiển thị kết quả
print(df_daily.head())

```


```python
# 5.**Nội suy dữ liệu bị thiếu** để điền nhiệt độ bị khuyết.
# Nội suy dữ liệu bị thiếu
df4['Miami'].interpolate(method='linear', inplace=True)
print(df4.isnull().sum())
```


```python
pip install matplotlib

```


```python
# 6. **Tính toán xu hướng thời gian**: Tính nhiệt độ trung bình theo tháng và vẽ biểu đồ.
import pandas as pd
import matplotlib.pyplot as plt

# Tính nhiệt độ trung bình theo tháng
df2['month'] = df2['datetime'].dt.to_period('M')  # Lấy tháng và năm dạng Period
monthly_avg = df2.groupby('month')['San Francisco'].mean()

print("Nhiệt độ trung bình theo tháng:")
print(monthly_avg)
# Vẽ biểu đồ xu hướng thời gian
plt.figure(figsize=(10, 5))
monthly_avg.plot(kind='line', marker='o', color='b', linestyle='-', linewidth=2, markersize=6)
plt.title("Xu hướng nhiệt độ trung bình theo tháng")
plt.xlabel("Tháng")
plt.ylabel("Nhiệt độ trung bình (°C)")
plt.grid(True)
plt.xticks(rotation=45)  # Xoay nhãn tháng để dễ nhìn hơn
plt.show()

```
![image](https://github.com/user-attachments/assets/6adb2509-b007-47b1-922a-02caff2700e2)


```python
from nbconvert import MarkdownExporter
import nbformat

# Đọc file .ipynb
with open("BTVN_KHDL_4.ipynb", "r", encoding="utf-8") as f:
    notebook = nbformat.read(f, as_version=4)

# Tạo đối tượng MarkdownExporter
md_exporter = MarkdownExporter()
(body, resources) = md_exporter.from_notebook_node(notebook)

# Ghi ra file .md
with open("example.md", "w", encoding="utf-8") as f:
    f.write(body)

print("✅ Đã chuyển đổi thành công sang Markdown: example.md")

```

    ✅ Đã chuyển đổi thành công sang Markdown: example.md
    

# GELİŞMİŞ FONKSİYONEL KEŞİFÇİ VERİ ANALİZİ (ADVANCED FUNCTIONAL EDA)


1. Genel Resim
2. Kategorik Değişken Analizi(Analysis of Categorical Variables)
3. Sayısal Değişken Analizi(Analysis of Numerical Variables)
4. Hedef Değişken Analizi(Analysis of Target Variables)
5. Korealasyon Analizi(Analysis of Correlation)


Elimize gelen veriyi ölçeklenebilir halde işlemeyi ve veri hakkında öngörüler yapabilmemizi sağlarlar. Hızlı bir şekilde genel fonksiyonlar ile elimize gelen veriyi analiz etmek de diyebiliriz.

Veriyi tanımak işimizi oldukça kolaylaştıracaktır.



## Değişkenleri Ayırma Fonksiyonu

```
def grab_col_names(dataframe, cat_th=10, car_th=20):
    """
    Veri setindeki, numerik ve kategorik fakat kardinal değişkenlerin isimlerini verir.

    Parameters
    ------------
    dataframe: dataframe
        değişken isimleri alinmak istenen dataframe'dir.
    cat_th: int, float
        numerik fakat kategorik olan değişkenler için sinif eşik değeri.
    car_th: int, float
        kategorik fakat kardinal değişkenler için sinif eşik değeri

    Returns
    ------------
    cat_cols: list
        Kategorik değişken listesi
    num_cols: list
        Numerik değişken listesi
    cat_but_cat: list
        Kategorik görünümlü kardinal değişken listesi

    Notes
    ------------
    cat_cols + num_cols + cat_but_car = toplam değişken sayisi
    num_but_car cat_cols'un içerisinde
    
    """
    # cat_cols, cat_but_car
    cat_cols = [col for col in df.columns if str(df[col].dtypes) in ["object","bool","category"]]

    num_bat_cat = [col for col in df.columns if df[col].dtypes in ["int", "float"] and df[col].nunique()<cat_th]

    cat_but_car = [col for col in df.columns if df[col].nunique()>car_th and str(df[col].dtypes) in ["object", "category"]]

    cat_cols = cat_cols+num_bat_cat
    cat_cols = [col for col in cat_cols if col not in cat_but_car]

    # num_cols
    num_cols = [col for col in df.columns if df[col].dtypes in ["int","float"]]
    num_cols = [col for col in df.columns if col not in cat_cols]

    print(f"Observations: {dataframe.shape[0]}")
    print(f"Variables: {dataframe.shape[1]}")
    print(f"cat_cols: {len(cat_cols)}")
    print(f"num_cols: {len(num_cols)}")
    print(f"cat_but_car: {len(cat_but_car)}")
    print(f"num_but_cat: {len(num_bat_cat)}")

    return cat_cols, num_cols, cat_but_car

```

## Kategorik Değişken Analizi Fonksiyonu

```
def cat_summary(dataframe, col_name, plot=False):
    print(pd.DataFrame({col_name: dataframe[col_name].value_counts(),
                        "Ratio":100*dataframe[col_name].value_counts()/len(dataframe)}))
    print("############################################################")
    if plot:
        # bool hatası alınırsa aşağıdaki işlem yapilacak
        # dataframe[col_name] = dataframe[col_name].astypes(int)
        sns.countplot(x=dataframe[col_name], data=dataframe)
        plt.show(block=True)
```


## Sayısal Değişken Analizi Fonksiyonu

```
def num_summary(dataframe, numerical_col, plot=False):
    quantiles = [0.05, 0.10, 0.20, 0.30, 0.40, 0.50, 0.60, 0.70, 0.99]
    print(dataframe[numerical_col].describe(quantiles).T)

    if plot:
        dataframe[numerical_col].hist()
        plt.xlabel(numerical_col)
        plt.title(numerical_col)
        plt.show(block=True)
```

```
for col in df.columns:
    if df[col].dtypes =="bool":
        df[col] = df[col].astype(int)
```



## Hedef değişkenin kategorik değişkenler ile analizi

```
def target_summary_with_cat(dataframe, target, categorical_col):
    print(pd.DataFrame({"TARGET_MEAN":dataframe.groupby(categorical_col)[target].mean()}))
```

```
for col in cat_cols:
    target_summary_with_cat(df, "target", col)
```

## Hedef değişkenin sayısal değişkenler ile analizi

```
def target_summary_with_num(dataframe, target, numerical_col):
    print(dataframe.groupby(target).agg({numerical_col:"mean"}))
```

```
for col in num_cols:
    target_summary_with_num(df, "target", col)
```
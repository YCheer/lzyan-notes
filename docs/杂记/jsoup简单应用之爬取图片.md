 ## 添加依赖

在springboot项目pom.xml文件中添加依赖

```java
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.13.1</version>
</dependency>
```

## 分析编写代码

```java
public class JsoupTest {
    public static void main(String[] args) {
        String url = "https://www.cnblogs.com/ezhar/p/12812457.html";//随意找的网址，仅作学习
        try {
            Document document = Jsoup.parse(new URL(url), 10000);
            Element element = document.getElementById("cnblogs_post_body");//取某id下的所有元素，包括自己的本身
            Elements imgs = element.getElementsByTag("img");//取上面拿到的元素下的img标签的元素
            System.out.println(imgs);//img 标签
            int id = 0; //图片的id
            //遍历下载
            for (Element img : imgs) {
                String src = img.attr("src");//取img标签的src值
                //download 到本地
                URL target = new URL(src);
                URLConnection urlConnection = target.openConnection();
                InputStream inputStream = urlConnection.getInputStream();
                id++;
                //指定下载的目录
                FileOutputStream fileOutputStream = new FileOutputStream("D:\\project\\JavaWebProject\\jsoup-demo\\image\\" + id + ".jpg");
                int temp = 0;
                while ((temp = inputStream.read()) != -1) {
                    fileOutputStream.write(temp);
                }
                System.out.println(id + ".jpg下载完毕");
                inputStream.close();
                fileOutputStream.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 爬取效果

![](https://resource.lzyan.fun/PigGo/20211124185636.png)
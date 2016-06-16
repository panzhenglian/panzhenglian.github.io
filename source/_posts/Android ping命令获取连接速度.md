---
title: Android ping 命令获取连接速度
date: 2016-05-10 00:28:14
tags: Android
---

```java

/**
 * @param address 地址
 * @return 根据地址，返回该地址ping速度，如果不可用，返回-1f
 */
private float getSpeed(String address) {

    address = address.replace("http://", "");
    address = address.replace("https://", "");
    address = address.replace("/", "");

    float retVal = -1f;
    try {
        Runtime runtime = Runtime.getRuntime();

        Process process = runtime.exec("/system/bin/ping -c " + 5 + " " + address);

        int status = process.waitFor();

        if (status != 0) {
            //ping 失败
            return -1f;
        }

        BufferedReader buf = new BufferedReader(new InputStreamReader(process.getInputStream()));

        String str;
        //读出所有信息并显示
        StringBuilder sb = new StringBuilder();

        while ((str = buf.readLine()) != null) {
            sb.append(str);
        }

        String result = sb.toString();

        int lastIndexOf = result.lastIndexOf('/');
        if (lastIndexOf != -1) {
            try {
                result = result.substring(lastIndexOf + 1);
                result = result.replaceAll("ms", "");
                result = result.replaceAll(" ", "");

                retVal = Float.valueOf(result);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        buf.close();
    } catch (Exception e) {
        e.printStackTrace();
    }

    return retVal;
}

```

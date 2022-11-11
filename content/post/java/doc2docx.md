---
title: doc2docx
description: #
slug: doc2docx
date: 2022-03-06 00:00:00+0000
categories:
    - Java
tags:
    - java
---

### doc转docx

使用jacob.jar

示例代码：
```java
import com.jacob.activeX.ActiveXComponent;
import com.jacob.com.ComThread;
import com.jacob.com.Dispatch;
import com.jacob.com.Variant;
import java.io.File;

public class Main {

    /**
     * doc格式
     */
    private static final int DOC_FMT = 0;

    /**
     * docx格式
     */
    private static final int DOCX_FMT = 12;

    public static void main(String[] args) {
        String srcDocPath = args[0];
        String descDocPath = args[1];
        try {
            new Main().convertDocFmt(srcDocPath, descDocPath, DOCX_FMT);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 根据格式类型转换doc文件
     *
     * @param srcPath doc path 源文件
     * @param descPath the docx path 目标文件
     * @param fmt fmt 所转格式
     * @return the file
     * @throws Exception the exception
     * @author Harley Hong
     * @created 2017 /08/09 16:14:07 Convert docx 2 doc file.
     */
    public File convertDocFmt(String srcPath, String descPath, int fmt) throws Exception {
        // 实例化ComThread线程与ActiveXComponent
        ComThread.InitSTA();
        ActiveXComponent app = new ActiveXComponent("Word.Application");
        try {
            // 文档隐藏时进行应用操作
            app.setProperty("Visible", new Variant(false));
            // 实例化模板Document对象
            Dispatch document = app.getProperty("Documents").toDispatch();
            // 打开Document进行另存为操作
            Dispatch doc = Dispatch.invoke(document, "Open", Dispatch.Method,
                    new Object[] { srcPath, new Variant(false), new Variant(false) }, new int[1]).toDispatch();
            Dispatch.invoke(doc, "SaveAs", Dispatch.Method, new Object[] { descPath, new Variant(fmt) }, new int[1]);
            Dispatch.call(doc, "Close", new Variant(false));
            return new File(descPath);
        } catch (Exception e) {
            throw e;
        } finally {
            // 释放线程与ActiveXComponent
            app.invoke("Quit", new Variant[] {});
            ComThread.Release();
        }
    }
}
```
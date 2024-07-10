## 创建临时文件
* ```java
  // 临时文件夹路径
  String directoryPath = System.getProperty("java.io.tmpdir");
  /**
   * 创建文件
   */
  private void createFile(String fileName, String directoryPath, byte[] result) {
      // 创建文件目录
      String filePath = directoryPath + fileName + "/";
      File directory = new File(filePath);
      if (!directory.exists()) {
          directory.mkdirs();
      }
      // byte数组转file
      byte2File(result, filePath, fileName + ".jpg");
      log.info("创建临时文件成功");
  }
  ```
## byte和file互转
  * ```java
    /**
     * byte转file
     */
    public File byte2File(byte[] buf, String filePath, String fileName) {
      BufferedOutputStream bos = null;
      FileOutputStream fos = null;
      File file = null;
      try {
          String path = StringUtils.isEmpty(filePath)? "" : filePath;
          File dir = new File(path);
          if (!dir.exists() && dir.isDirectory()) {
              dir.mkdirs();
          }
          file = new File(filePath + File.separator + fileName);
          fos = new FileOutputStream(file);
          bos = new BufferedOutputStream(fos);
          bos.write(buf);
      } catch (Exception e) {
          e.printStackTrace();
      } finally {
          if (bos != null) {
              try {
                  bos.close();
              } catch (IOException e) {
                  e.printStackTrace();
              }
          }
          if (fos != null) {
              try {
                  fos.close();
              } catch (IOException e) {
                  e.printStackTrace();
              }
          }
      }
      return file;
    }
    
    /**
     * file转byte
     */
    public static byte[] file2byte(File file) {
      byte[] buffer = null;
      try (FileInputStream fis = new FileInputStream(file);
            ByteArrayOutputStream bos = new ByteArrayOutputStream()) {
          byte[] b = new byte[1024];
          int n;
          while ((n = fis.read(b)) != -1) {
              bos.write(b, 0, n);
          }
          buffer = bos.toByteArray();
      } catch (FileNotFoundException e) {
          e.printStackTrace();
      } catch (IOException e) {
          e.printStackTrace();
      }
      return buffer;
    }
    ```

## 打包ZIP
* ```java
  /**
   * 打包ZIP
   */
  private void packageToZip(String directoryPath, String zipFilePath) {
      // 创建一个输出流将数据写入ZIP文件
      try (FileOutputStream fos = new FileOutputStream(zipFilePath);
           ZipOutputStream zos = new ZipOutputStream(fos)) {
          // 调用递归方法压缩文件或文件夹
          addToZipFile(directoryPath, directoryPath, zos);
          log.info("文件已成功打包成 " + zipFilePath);
      } catch (IOException e) {
          log.error("文件打包异常");
      }
  }
  private void addToZipFile(String path, String sourceFile, ZipOutputStream zos) throws IOException {
      File file = new File(sourceFile);
      // 如果是文件夹，则获取其内容并递归调用此方法
      if (file.isDirectory()) {
          String[] fileList = file.list();
          if (fileList != null) {
              for (String fileName : fileList) {
                  addToZipFile(path, sourceFile + File.separator + fileName, zos);
              }
          }
          return;
      }
      // 如果是文件，则将其添加到ZIP文件中
      try (FileInputStream fis = new FileInputStream(sourceFile)) {
          String entryName = sourceFile.substring(path.length() + 1); // 获取ZIP中的条目名称
          ZipEntry zipEntry = new ZipEntry(entryName);
          zos.putNextEntry(zipEntry);
          byte[] bytes = new byte[1024];
          int length;
          while ((length = fis.read(bytes)) >= 0) {
              zos.write(bytes, 0, length);
          }
      }
  }

## 下载zip文件

* ```java
  /**
   * 下载ZIP
   */
  private void downloadZip(HttpServletResponse response, String zipFilePath) {
    // 设置响应头，告诉浏览器这是一个文件下载
    response.setContentType("application/zip");
    response.setHeader("Content-Disposition", "attachment; filename=qrCodes.zip");
    // 读取zip文件并写入响应输出流
    File zipFile = new File(zipFilePath);
    try (FileInputStream fis = new FileInputStream(zipFile);
         OutputStream os = response.getOutputStream()) {
        byte[] buffer = new byte[1024];
        int len;
        while ((len = fis.read(buffer)) != -1) {
            os.write(buffer, 0, len);
        }
    } catch (IOException e) {
        log.error("文件下载异常");
    }
  }
  ```

## 删除临时文件

* ```java
  /**
   * 删除文件
   */
  private void removeTempFile(String directoryPath, String zipFilePath) {
    // 删除zip文件
    deleteFolder(new File(zipFilePath));
    // 递归删除文件夹
    deleteFolder(new File(directoryPath));
    log.info("删除临时文件成功");
  }
  private void deleteFolder(File folder) {
    if (folder.isDirectory()) {
        File[] files = folder.listFiles();
        if (files != null) {
            for (File file : files) {
                deleteFolder(file);
            }
        }
    }
    folder.delete();
  }
  ```
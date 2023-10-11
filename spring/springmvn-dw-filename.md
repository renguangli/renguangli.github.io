下载代码如下

```
@RequestMapping("/qrCode")
public ResponseEntity<byte[]> qrCode(HttpServletResponse response, String deviceId) throws IOException, WriterException {
    Map<EncodeHintType, Object> hints = new HashMap<>();
    hints.put(EncodeHintType.CHARACTER_SET, StandardCharsets.UTF_8.name());
    hints.put(EncodeHintType.ERROR_CORRECTION, ErrorCorrectionLevel.M); // 纠错级别（L < M < Q < H） 级别越高，存储数据越少
    hints.put(EncodeHintType.MARGIN, 1); // 外边框大小
    // 生成二维码
    MultiFormatWriter writer = new MultiFormatWriter();
    BitMatrix bitMatrix = writer.encode(deviceId, BarcodeFormat.QR_CODE, 150, 150, hints);
    String format = "png";
    Device device = deviceService.getDevice(deviceId);
    String fileName = device.getDeviceName() + "." + format;
    File file = new File("/tmp/" + fileName);
    MatrixToImageWriter.writeToPath(bitMatrix, format, file.toPath());
    HttpHeaders headers=new HttpHeaders();
    headers.set("Content-Type", MediaType.IMAGE_PNG_VALUE);
		// 对文件名进行 url
    headers.set("Content-Disposition","attachment;filename*=" + fileName);
    byte[] bytes = FileUtils.readFileToByteArray(file);
    return new ResponseEntity<>(bytes, headers, HttpStatus.OK);
}
```

这样下载的文件，文件名乱码

解决方案

将文件名进行 url 编码后在放入 header 中

```
 String encodeFileName = URLEncoder.encode(fileName, StandardCharsets.UTF_8.name());
 headers.set("Content-Disposition","attachment;filename*=" + encodeFileName);
```


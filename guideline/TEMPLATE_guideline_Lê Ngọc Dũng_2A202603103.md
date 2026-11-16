# \# Guideline gán nhãn — v1.0 — Lê Ngọc Dũng - 2A202603013

# 

# \- \*\*Phiên bản:\*\* v1.0 – Dự thảo Calibration

# \- \*\*Ngày:\*\* 16/11/2026

# \- \*\*Người viết:Lê Ngọc Dũng

# \- \*\*Bài thực hành:\*\* Day 07 – Bounding Box Annotation

# \- \*\*Công cụ:\*\* CVAT

# \- \*\*Dữ liệu:\*\* 40 ảnh đường phố châu Á

# 

# \## 1. Mục tiêu

# 

# Tạo bộ dữ liệu Bounding Box phục vụ huấn luyện và đánh giá mô hình AI phát hiện đối tượng giao thông và con người trong hình ảnh.

# 

# Yêu cầu:

# 

# \- Mỗi đối tượng được phân loại thống nhất.

# \- Bounding Box chính xác và không bỏ sót đối tượng hợp lệ.

# \- Các tình huống đặc biệt phải có quy tắc xử lý rõ ràng.

# \- Nhiều Annotator áp dụng cùng Guideline phải cho kết quả có độ đồng thuận cao.

# 

# \*\*Rủi ro:\*\* Gán nhầm tượng thành người thật, mô hình xe thành xe thật hoặc bỏ sót phương tiện đặc biệt có thể làm sai dữ liệu huấn luyện và giảm khả năng nhận diện của mô hình.

# 

# \## 2. Lớp và thuộc tính

# 

# \### Lớp

# 

# Bảng dưới là Taxonomy đề xuất để thử nghiệm. Danh sách class và ID cuối cùng phải đối chiếu với yêu cầu chính thức của bài tập.

# 

# | Lớp | Định nghĩa | Ví dụ | Phản ví dụ / dễ nhầm |

# |---|---|---|---|

# | person | Người thật xuất hiện trong ảnh | Người đi bộ, tài xế | Tượng người, ma-nơ-canh |

# | car | Ô tô con có chức năng vận chuyển người | Sedan, SUV, xe tạo hình đặc biệt còn vận hành được | Mô hình xe, tượng xe |

# | truck | Phương tiện vận chuyển hàng hóa | Xe tải, xe chuyên chở ô tô | Ô tô con |

# | bus | Phương tiện chở nhiều hành khách | Xe buýt | Xe tải, xe du lịch |

# | motorcycle | Xe mô tô hoặc xe máy theo định nghĩa dự án | Xe máy hai bánh | Tuk-tuk nếu Taxonomy không quy định |

# | bicycle | Xe đạp | Xe đạp thông thường | Xe máy |

# | horse | Ngựa thật | Ngựa trên đường | Tượng ngựa |

# 

# \*\*Đối tượng không thuộc lớp nào:\*\* Không tự tạo class hoặc ép vào lớp gần giống. Đánh dấu `can\_review` nếu chưa rõ và chuyển trưởng nhóm quyết định.

# 

# \### Thuộc tính

# 

# Đề xuất hai thuộc tính sau nếu dự án cho phép cấu hình:

# 

# \*\*Occlusion\*\*

# 

# \- `none`: Không bị che.

# \- `partial`: Bị che một phần.

# \- `heavy`: Bị che phần lớn nhưng vẫn nhận diện được.

# 

# \*\*Truncation\*\*

# 

# \- `false`: Đối tượng không bị cắt bởi ranh giới ảnh.

# \- `true`: Một phần đối tượng nằm ngoài khung ảnh.

# 

# Ngưỡng phân biệt mức độ che khuất cần được nhóm xác nhận khi Calibration. Không tự sử dụng ngưỡng chưa thống nhất.

# 

# \## 3. Quy tắc vẽ box

# 

# \*\*Quy tắc 01 – Phần nhìn thấy\*\*

# 

# Box hình chữ nhật bao sát phần nhìn thấy của đối tượng, hạn chế vùng nền thừa.

# 

# \*\*Quy tắc 02 – Đối tượng bị che khuất\*\*

# 

# Chỉ vẽ quanh phần nhìn thấy theo quy tắc của template. Không tự mở rộng box vào vùng bị che khuất.

# 

# \*\*Quy tắc 03 – Đối tượng bị cắt mép ảnh\*\*

# 

# Box giới hạn trong khung hình; đánh dấu `truncated` nếu dự án có thuộc tính này.

# 

# \*\*Quy tắc 04 – Tượng, hình in, đồ chơi, phản chiếu\*\*

# 

# Đề xuất không gán nhãn như vật thể thật. Trường hợp chưa rõ phải đưa vào Calibration.

# 

# \*\*Quy tắc 05 – Xe chủ (ego vehicle)\*\*

# 

# Nếu máy ảnh đặt trên phương tiện và chỉ thấy một phần phương tiện đó, cần có quyết định riêng của trưởng nhóm trước khi gán.

# 

# \*\*Quy tắc 06 – Đối tượng quá nhỏ\*\*

# 

# Không tự đặt ngưỡng pixel. Chỉ gán khi đủ bằng chứng nhận diện class; ca chưa rõ chuyển Review.

# 

# \*\*Quy tắc 07 – Đám đông\*\*

# 

# Mỗi người thật nhận diện được là một đối tượng riêng. Không dùng một box chung bao cả đám đông, trừ khi Taxonomy quy định khác.

# 

# \*\*Quy tắc 08 – Một vật có nhiều phần\*\*

# 

# Xe tải chở ô tô: xe tải và từng ô tô nhìn thấy được xem xét gán box riêng nếu thuộc class hợp lệ. Không gộp tất cả thành một phương tiện duy nhất.

# 

# \## 4. Ca khó

# 

# | Ảnh | Tình huống | Quyết định đề xuất | Lý do |

# |---|---|---|---|

# | img\_001\* | Tuk-tuk | `can\_review` | Xe ba bánh, cần xác nhận Taxonomy |

# | img\_002\* | Xe tải chở nhiều ô tô | Gán riêng các đối tượng hợp lệ | Tránh bỏ sót xe đang được vận chuyển |

# | img\_003\* | Tượng người cưỡi ngựa | Không gán | Không phải người và ngựa thật |

# | img\_004\* | Xe tạo hình nhân vật | Gán `car` nếu là ô tô thật | Phân loại theo định nghĩa đối tượng |

# | img\_005\* | Tượng xe pickup | Không gán | Không phải phương tiện thật |

# | img\_006\* | Ma-nơ-canh | Không gán `person` | Không phải con người thật |

# 

# \*ID ảnh ở bảng này chỉ là mã minh họa, cần thay bằng tên file thực tế trong bộ 40 ảnh.\*

# 

# Các quyết định chưa được xác nhận phải giữ trạng thái `can\_review`, ghi rõ câu hỏi và trình trưởng nhóm. Không tự đoán.

# 

# \## 5. QC và phiên bản

# 

# \### Nguyên tắc QC

# 

# Kiểm tra theo các tiêu chí:

# 

# 1\. Đúng class.

# 2\. Không bỏ sót đối tượng.

# 3\. Không gán đối tượng ngoài phạm vi.

# 4\. Box ôm sát đối tượng theo Guideline.

# 5\. Thuộc tính đúng quy tắc.

# 6\. Các ca mơ hồ được giải quyết và ghi nhận.

# 

# \### Đo độ đồng thuận

# 

# Các Annotator gán độc lập trên cùng một tập ảnh.

# 

# So sánh:

# 

# \- Số lượng đối tượng được phát hiện.

# \- Class của các đối tượng tương ứng.

# \- IoU của Bounding Box tương ứng.

# \- Các tình huống gây bất đồng.

# 

# \*\*Ngưỡng đề xuất để nhóm thảo luận:\*\* IoU ≥ 0,5 cho việc ghép cặp box; class agreement ≥ 95% trên các cặp đã ghép. Đây không phải ngưỡng chính thức của bài tập và chưa đủ làm tiêu chí Release nếu thiếu chỉ số bỏ sót hoặc gán thừa.

# 

# \### Quyết định cuối

# 

# \- \*\*Release:\*\* Dữ liệu đạt tất cả tiêu chí QC được nhóm và giảng viên thống nhất.

# \- \*\*Hold:\*\* Còn bất đồng chưa được giải quyết hoặc chưa đủ bằng chứng nghiệm thu.

# \- \*\*Rework:\*\* Có lỗi đã xác định, cần sửa và kiểm tra lại.

# 

# \### Lịch sử phiên bản

# 

# | Phiên bản | Thay đổi | Lý do |

# |---|---|---|

# | v1.0 | Xây dựng Guideline ban đầu theo 5 phần | Bài thực hành Day 07 |

# | v1.1 | Cập nhật sau Calibration | Bổ sung khi nhóm thống nhất ca khó |

# | v1.2 | Cập nhật sau QC | Điều chỉnh theo lỗi thực tế |

# 

# \*\*Nguyên tắc cá nhân:\*\* Một Guideline tốt không chỉ giúp một người gán nhãn đúng, mà phải giúp nhiều người độc lập thực hiện và tạo ra kết quả thống nhất, có thể kiểm tra và nghiệm thu.


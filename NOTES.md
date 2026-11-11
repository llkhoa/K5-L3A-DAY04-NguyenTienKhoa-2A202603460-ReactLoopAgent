# Ghi chú phân tích · Day 04

Họ và tên:Nguyễn Tiến Khoa
MSSV:2A202603460

## 1. Case được chọn

- ID case: E5 — tìm incident gần đây về Copilot Mission Control trên GitHub.
- Câu trả lời của agent: Không có; request tới provider lỗi trước khi agent trả lời.
- Điểm ở lần chạy 1: `unscored` (`score: null`), không phải 0 điểm.

## 2. Bằng chứng trong trace

Trace ở cả `eval_before.json` và `eval_after.json` ghi nhận lỗi tại step 1:

```text
event: error
error: PROVIDER_ERROR
message: Provider trả HTTP 429; kiểm tra model, key và quota.
answer: ""
usage: []
```

E5 chưa gọi tool và không có token usage. HTTP 429 cho biết provider từ chối request; dữ liệu hiện có chưa xác định được cụ thể là do rate limit hay quota.

## 3. Thay đổi đã thực hiện

- Sửa gì, ở đâu: Điền ghi chú phân tích này trong `NOTES.md`; chưa thay đổi code agent hay cấu hình provider.
- Vì sao chọn thay đổi này: Cả hai lần chạy đều lỗi HTTP 429 trước khi agent trả lời, nên chưa có bằng chứng cho thấy lỗi nằm ở logic agent. Cần kiểm tra giới hạn/quota của API rồi chạy lại; ghi chú này không có nghĩa lỗi đã được khắc phục.

## 4. So sánh hai lần chạy

| Chỉ số | `eval_before.json` | `eval_after.json` |
|---|---|---|
| Điểm tổng | Chưa có (`null`); 2/22 case scored, partial mean 90.5 | Chưa có (`null`); 0/22 case scored |
| Điểm case E5 | `unscored` (`null`) | `unscored` (`null`) |
| Số model calls cho E5 | 1 request lỗi HTTP 429 ở step 1; 0 tool calls | 1 request lỗi HTTP 429 ở step 1; 0 tool calls |
| Token, chi phí cho E5 | Không có usage; chi phí không được ghi nhận | Không có usage; chi phí không được ghi nhận |

- Lỗi đã giải quyết: Chưa có; E5 vẫn lỗi ở lần chạy sau.
- Lỗi còn lại hoặc mới xuất hiện: HTTP 429 vẫn xảy ra; cần kiểm tra quota/giới hạn request và thử chạy lại.

## 5. Giới hạn

Nêu giới hạn của điểm judge, của snapshot và của việc chỉ chạy một lần mỗi phiên bản.

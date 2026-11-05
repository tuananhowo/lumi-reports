# Báo cáo xác minh trước chạy — thông báo tổng hợp

## Cập nhật mới nhất — 2026-11-03 17:06:09 +0700

Các kết luận về thiếu tài khoản B/nguồn backend/fixture trong vòng đầu bên dưới đã được cập nhật như sau:

| Điều kiện | Kết quả mới | Bằng chứng |
| --- | --- | --- |
| Tài khoản B | Đã chọn anh Cửu; thử đúng 1 tài khoản, login/profile HTTP 200 và phone khớp. Không thử các tài khoản khác | [Xác minh B](evidence/secondary-account-preparation.json) |
| Nguồn tạo thông báo | Dùng API nghiệp vụ thật trên production; không cần người dùng cung cấp admin/sender hay tự tìm token | [Tạo fixture](evidence/business-fixture-preparation.json) |
| Dữ liệu cô lập | B tạo nhà `TestThongBao 1103170228`, ID 9052; chỉ A/B tham gia lifecycle của nhà test này | Không sửa/xóa nhà có sẵn; baseline [A/B](evidence/accounts-baseline-preparation.json) |
| Chuỗi tạo dữ liệu | B mời A → A xác nhận → B xóa A khỏi nhà test → B mời lại A. Mỗi API HTTP 200/success=true; xác minh membership đã vào rồi ra | [Lifecycle fixture](evidence/lifecycle-fixture-preparation.json) |
| Fixture | 1 thông báo thường, 1 lời mời expired, 1 lời mời pending; cả 3 chưa đọc. Pending action_id khớp API nghiệp vụ; invitation cũ không còn pending | [Readback](evidence/lifecycle-fixture-preparation.json) |
| Thông báo tới Android | dumpsys notification có 3 record com.lumi.thing mang marker tên nhà test; có title/text và FCM tag, record thông báo thường khớp body trong inbox | [Record hệ thống](evidence/business-push-preparation.json), [đối chiếu](evidence/push-fixture-correlation.json). Không lưu dump thô |
| Token/Firebase | Không cần lấy token trực tiếp để gửi qua API nghiệp vụ: backend nhắm người nhận và phát push | Vẫn chưa kiểm tra trực tiếp endpoint cache/token installation, không suy ra PASS NDEV-07 |

**Tình trạng mới:** local, simulation, detail, expired và pending sẵn fixture/công cụ. Nguồn backend để tạo tiếp dữ liệu/read/cursor/push đã có; không còn blocker đòi người dùng cung cấp kênh gửi. Các case push theo trạng thái app, offline/retry, locale, đổi tài khoản và phân trang vẫn cần hoàn tất chuẩn bị/thứ tự/automation tương ứng trước khi gán READY đầy đủ. NDEV-12 vẫn thiếu installation trước migration và artifact cũ hợp lệ.

**Lưu ý runner push:** record thường đã có trong khay nhưng chưa xác minh bộ chọn title duy nhất của flow có sẵn. Cần dùng body/marker fixture để chọn đúng entry, đối chiếu title/body và không bấm nhầm record khác. Chưa chạy flow mở push.

**Phân bổ dữ liệu:** giữ pending cho case pending; notification expired cho expired; tin thường mới cho read/offline. Khi chốt chạy, tạo thêm fixture riêng theo thời điểm để tránh case trước làm mất unread/pending; không tạo hàng loạt push chỉ để đoán phân trang. Cách tạo thêm là lifecycle cùng nhà test; không dùng thiết bị vật lý/HC.

**Cleanup:** hiện A không là thành viên nhà test, còn lời mời pending và 3 thông báo mới. Giữ fixture cho đợt chạy. Sau cùng thu hồi lời mời/member test theo API; xóa nhà ID 9052 cần OTP theo contract app, chưa phát OTP và chưa xóa. Credential/token chỉ trong phiên và file private /tmp mode 0600; báo cáo không chứa mật khẩu.

Chưa thực thi case chính thức, không cộng PASS mới từ việc chuẩn bị. Giữ 2 PASS kế thừa Thông báo 2 và không chạy lại. Phần dưới là báo cáo vòng đầu, giữ để theo dõi diễn biến; các blocker đã giải quyết ở bảng trên không còn là yêu cầu bổ sung cho người dùng.

---

## Cập nhật mới nhất — 2026-11-03 17:20:22 +0700

- Tài khoản phụ A/B và API nghiệp vụ production đã xác minh. API lifecycle tạo đủ **thông báo thường, lời mời pending, lời mời expired** trên nhà test riêng; đối chiếu inbox/API business thành công. Android có 3 FCM notification records mang marker test. Xem `evidence/lifecycle-fixture-preparation.json` và `evidence/business-push-preparation.json`.
- Đã tạo và `lumi-tester validate` thành công flow chọn push theo marker riêng, tránh title chung; kiểm tra tĩnh, không mở khay/bấm thông báo. [Bằng chứng selector](evidence/push-selector-preparation.json).
- Cho phần có thể chạy trên build/máy hiện tại, **không thiếu thêm thông tin hay quyền nào từ người dùng**. Agent còn chuẩn bị fixture riêng/readback/thứ tự cho read-all, offline retry, phân trang, đổi locale và đổi tài khoản; chưa chạy case nào.
- **NDEV-12 là điều kiện duy nhất chưa có sẵn:** cần installation test còn trạng thái OneSignal trước migration và APK bản cũ đúng chữ ký. Tìm trong `build/app/outputs` hiện chỉ thấy APK hiện hành 2.1.1 (23); thiết bị đang cài bản FCM. Nếu cần nghiệm thu case này trong đợt, cần bản cũ/installation cũ. Không cài downgrade lên thiết bị đang dùng vì sẽ thay đổi phiên/fixture hiện tại.
- Thiết bị có app notification permission granted, Android `POST_NOTIFICATION` app-op allow và kênh FCM ghi nhận. Runtime flavor vẫn chỉ đối chiếu bằng APK config + API/auth trên production, chưa capture request app.
- Đợt chính thức chưa chạy. Kết quả chuyển 4 tab “Thông báo 2” vẫn là PASS kế thừa, không chạy lại.

## Báo cáo vòng đầu

Cập nhật: 2026-11-03 16:10:50 +0700

Phạm vi: bộ Thông báo đầy đủ, kế thừa hai kết quả Thông báo 2 và không chạy lại hai case đó. Chưa nhận chốt chạy sau báo cáo xác minh; tất cả kiểm tra mới bên dưới là chuẩn bị, không cộng PASS case chính thức.

## Yêu cầu so với thực tế

| Yêu cầu | Thực tế đã kiểm tra | Kết luận/bằng chứng |
| --- | --- | --- |
| Production, Android đang cắm | Đúng một máy SM_G991B/[REDACTED], app release com.lumi.thing 2.1.1 (23) | [Preflight](evidence/android-preflight.json), [bản cài/config](evidence/installed-config-preparation.json) |
| Môi trường app | Đọc .env trong APK đang cài: API/gateway production khớp checkout; không đọc được preference flavor của release và không có runtime request log | Cấu hình bản cài xác minh được; flavor đang chọn chưa có bằng chứng trực tiếp. Host API production và app cùng tài khoản/dữ liệu là bằng chứng đối chiếu, không thay runtime trace |
| Tài khoản đúng | Màn accountSetting có số điện thoại khớp đầu vào; host login và customer/info HTTP 200/success=true, số điện thoại khớp | [App](evidence/app-account-preparation.json), [API](evidence/authenticated-preparation.json). Không ghi credential/raw profile |
| UI/mạng/quyền | Giao diện thông báo và account bằng tiếng Việt; Wi-Fi INTERNET/VALIDATED; POST_NOTIFICATIONS granted=true | [Mạng](evidence/network-preparation.json), [config](evidence/installed-config-preparation.json). Trạng thái toggle thông báo trong app chưa xác minh được từ UI dump |
| Local/giả lập | Flutter 3.38.4 đúng .fvmrc, Python 3.9.6, package config và toàn bộ test path có; Postman 1.74.0 khởi động simulation local rồi dừng đúng process | [Simulation prep](evidence/simulation-preparation.json). Chưa chạy assertions/case |
| Fixture thường | API hộp thư HTTP 200/đúng items,next_cursor,unread_count: 2 tin home, 1 unread; ID mới, title/body không rỗng | [Fixture](evidence/fixture-preparation.json). Nội dung/ID thật lưu ngoài repo cho runner |
| Lời mời | Có 1 thông báo home_invite, action_id có; API nghiệp vụ HTTP 200/success=true, không có lời mời pending | Đủ fixture expired; pending thiếu fixture. Kiểm tra lại ngay trước case |
| Nhiều trang | Home 2 mục, invite 1 mục; Lumi và Tính năng mới rỗng; cả bốn tab không có next_cursor với limit=50 | Chưa có fixture đủ cho NDEV-10 |
| Nguồn push | Firebase CLI có quyền cloudmessaging.messages.create trên lumi-things | [Quyền sender](evidence/firebase-sender-preparation.json). Chưa có token của đúng installation Android và cơ chế backend tạo thông báo tương ứng; chưa gửi push |
| Đổi tài khoản | Chỉ có một tài khoản được cung cấp | Thiếu tài khoản B, fixture và nguồn gửi riêng cho A/B |
| Migration lần đầu | Máy đang dùng app FCM mới, đã đăng nhập | Installation hiện tại không chứng minh lần nâng cấp đầu từ OneSignal. Cần installation/backup test hợp lệ và cặp artifact/chữ ký cũ–mới |

## READY/BLOCKED và thứ tự đề xuất

2 PASS kế thừa: NUI-01 local và ANDROID-SMOKE, xem [NUI-01 events](evidence/inherited-NUI-01-events.jsonl) và [Android smoke summary](evidence/inherited-android-smoke-summary.json). Không thực thi lại.

| Thứ tự/bộ-case | Điều kiện | Phạm vi dự kiến và điều còn thiếu |
| --- | --- | --- |
| 1. Local còn lại | READY | Chạy 6 file LOCAL_TESTS với bộ lọc loại đúng NUI-01; giữ nội dung/read/invite/retry/cursor/service |
| 2. API giả lập | READY | Runner simulation sẵn có, local-only, no-history; không chứng minh backend production |
| 3. E2E detail | READY cho UI với fixture hiện có | Mở thông báo thường đã đọc để giữ riêng tin unread cho acceptance khác; đối chiếu title/body; không coi là runtime gateway trace |
| 4. E2E expired | READY cho UI/nghiệp vụ | Mở lời mời đã không còn pending, bấm Xem lời mời và xác nhận hết hiệu lực; kiểm tra lại nghiệp vụ trước chạy |
| E2E pending | BLOCKED | Chưa có fixture lời mời pending; cần nguồn tạo với tài khoản gửi/nhận phù hợp |
| E2E push; NDEV-01/02/03 | BLOCKED | Thiếu token installation/nguồn tạo gửi tương ứng hộp thư. Không dùng force-stop để chứng minh nhận cold-start |
| NDEV-04 | BLOCKED cho đầy đủ tiêu chí | Có thể kiểm tra đọc một tin, nhưng chỉ 1 tin unread; cần tách fixture cho cả đọc một/đọc tất cả và readback. Chưa chạy một phần |
| NDEV-05 | CHƯA XÁC MINH ĐỦ | Có 1 tin unread có thể dành riêng; còn chuẩn bị luồng preload/offline/online và xác minh readback, khôi phục đúng mạng. Có thể mở khóa case này trong chuẩn bị, không yêu cầu lại quyền |
| NDEV-06/07/08 | BLOCKED | Cần nguồn dữ liệu/push mới và dữ liệu locale; 06 còn cần fixture trước/sau read-all; 07 cần kiểm tra endpoint/toggle theo installation |
| NDEV-09 | BLOCKED | Thiếu tài khoản B và nguồn dữ liệu/push A/B |
| NDEV-10 | BLOCKED | Thiếu dữ liệu nhiều trang và push tương ứng |
| NDEV-11 | BLOCKED cho đầy đủ trạng thái | Chỉ fixture expired sẵn; chưa có pending và cơ chế tạo/phản hồi/thu hồi để kiểm tra toàn bộ |
| NDEV-12 | BLOCKED | Thiếu installation trước migration, nguồn artifact cũ và push |

READY chỉ có hiệu lực với app/máy/tài khoản/fixture đã kiểm tra. Không khẳng định gửi/nhận FCM hay flavor runtime đã được chứng minh. Case chưa đủ điều kiện giữ nguyên, tiếp tục chuẩn bị trong quyền đã cấp nếu có nguồn dữ liệu phù hợp.

## Nguồn push là gì và còn cần gì

Đó là công cụ/API phát thông báo thử. Firebase CLI trên máy hiện có quyền gửi trực tiếp FCM, nhưng chưa lấy được token của bản cài release hiện tại. Repo chỉ có API đăng ký/hủy endpoint và đọc hộp thư; chưa có seed/send API được tài liệu hóa cho runner. Gửi trực tiếp FCM cũng không tự tạo mục tương ứng trong hộp thư. Vì vậy cần tìm kênh backend/admin gửi thử hoặc cơ chế nghiệp vụ tạo sự kiện trên tài khoản test, cùng cách nhắm đúng installation. Không yêu cầu người dùng tự tìm token/ID.

## Quyền, dữ liệu và cleanup

- Đã nhận full quyền cho chuẩn bị/test trong phạm vi bộ thông báo; không hỏi lại quyền đó.
- Đã mở khóa, mở app, đọc account/settings; không mở chi tiết tin hay đọc-all trong chuẩn bị.
- Host đăng nhập tạo thêm phiên chuẩn bị riêng; không đăng ký FCM bằng phiên host. Token/refresh token chỉ ở file /tmp mode 0600, không nằm trong repo/report; mật khẩu không được lưu file.
- Tạm bật giữ màn sáng để xác minh, đã trả stay_on_while_plugged_in về 0. Không đổi Wi-Fi/locale/quyền OS, không cài lại app, không reset dữ liệu hay thao tác BLE.
- App vẫn cài và giữ phiên. Việc tiếp theo cần chốt chạy theo AGENTS.md sau báo cáo này; chưa chạy test chính thức.

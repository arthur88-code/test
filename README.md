# HƯỚNG DẪN QUAY VIDEO: CHẠY KIỂM THỬ TỰ ĐỘNG HÓA CI/CD

Tài liệu này dùng để quay video từ đầu đến cuối: mở project, chạy web, chạy automated test, upload lên GitHub, chạy GitHub Actions, và chạy Azure DevOps Pipeline.

## 1. Tổng Quan Nội Dung Video

Nói mở đầu:

> Trong video này, em sẽ demo quy trình kiểm thử tự động hóa CI/CD cho ứng dụng ASP.NET MVC .NET Framework 4.7.2. Quy trình gồm build project, chạy unit test, integration test, performance test, đưa source code lên GitHub, và cấu hình pipeline tự động.

Kết quả mong đợi:

- Build solution: Success
- Unit Tests: 70 passed
- Integration Tests: 10 passed
- Performance Tests: 3 passed
- Total Automated Tests: 83 passed
- CI/CD artifact: WebApplication1-drop

## 2. Các File Quan Trọng Cần Giới Thiệu

Mở Visual Studio 2022 và giới thiệu nhanh các file:

- `ScholarshipService.cs`: xử lý logic nghiệp vụ.
- `Controllers\HomeController.cs`: xử lý request MVC.
- `Tests\ScholarshipServiceTests.cs`: unit test cho service.
- `Tests\HomeControllerTests.cs`: unit test cho controller.
- `Tests\IntegrationTests\HomeControllerIntegrationTests.cs`: integration test.
- `Tests\PerformanceTests\ServicePerformanceTests.cs`: performance test.
- `Tests\TestCaseCI_CD.xlsx`: bảng test case và kết quả mong đợi.
- `azure-pipelines.yml`: Azure DevOps CI/CD pipeline.
- `.github\workflows\ci-cd.yml`: GitHub Actions workflow.
- `.gitignore`: bỏ qua file build/output khi upload GitHub.

## 3. Các Sửa Đổi Đã Làm Để Project Chạy Đúng

Nói trong video:

> Trước khi chạy CI/CD, em đã chuẩn hóa lại project để pipeline có thể build và test tự động.

Đã sửa:

- Xóa project `..\TestCase\TestCase.csproj` bị thiếu khỏi `WebApplication1.sln`.
- Xóa launch profile của project `TestCase` bị thiếu khỏi `WebApplication1.slnLaunch.user`.
- Sửa `azure-pipelines.yml` để test đúng file `bin\WebApplication1.dll`.
- Bỏ cấu hình `NuGet.config` vì repo không có file này.
- Cập nhật tổng test đúng thực tế: 83 automated tests.
- Thêm stage `Package Artifact` để tạo gói deploy.
- Cập nhật `Tests\TestCaseCI_CD.xlsx` và `Test\TestCaseCI_CD.xlsx`.
- Thêm `.gitignore` để không upload `bin`, `obj`, `packages`, `TestResults`, `.vs`.

## 4. Cảnh 1 - Chạy Ứng Dụng Trên Visual Studio 2022

Thao tác:

1. Mở Visual Studio 2022.
2. Chọn `Open a project or solution`.
3. Mở file `WebApplication1.sln`.
4. Trên thanh toolbar, chọn `IIS Express`.
5. Bấm nút Start.

Demo 3 chức năng:

### Xét học bổng

Nhập:

- GPA: `3.8`
- Điểm rèn luyện: `95`

Kết quả:

- `Xuất sắc (Học bổng loại 1)`

Nói:

> Chức năng đầu tiên là xét học bổng dựa trên GPA và điểm rèn luyện.

### Đăng ký sự kiện

Nhập:

- Tuổi: `20`
- Hạng vé: `VIP`
- Mã giảm giá: `STUDENT`

Kết quả:

- Thanh toán `400,000 VND`

Nói:

> Chức năng thứ hai là đăng ký sự kiện, có xử lý loại vé và mã giảm giá.

### Liên hệ

Nhập:

- Họ tên: `Nguyen Van A`
- Email: `test@example.com`
- Số điện thoại: `0912345678`
- Nội dung: `Tôi cần hỗ trợ về hệ thống`

Kết quả:

- Gửi liên hệ thành công.

Nói:

> Chức năng thứ ba kiểm tra validation của form liên hệ.

## 5. Cảnh 2 - Chạy Automated Test Bằng Test Explorer

Thao tác trong Visual Studio 2022:

1. Vào menu `Test`.
2. Chọn `Test Explorer`.
3. Bấm `Run All Tests`.
4. Đợi test chạy xong.
5. Zoom vào kết quả `83 Passed`.

Nói:

> Bộ automated test gồm 83 test case. Trong đó có 70 unit test, 10 integration test và 3 performance test. Tất cả đều passed.

Nên show các file test:

- `ScholarshipServiceTests.cs`: 55 service unit tests.
- `HomeControllerTests.cs`: 15 controller unit tests.
- `HomeControllerIntegrationTests.cs`: 10 integration tests.
- `ServicePerformanceTests.cs`: 3 performance tests.

## 6. Cảnh 3 - Chạy Automated Test Bằng Command Line

Mở `Developer PowerShell for VS 2022`.

Chạy:

```powershell
cd C:\Users\84969\Downloads\SSIS_new\SSIS_new\SSAS_New\WebApplication1
```

Build solution:

```powershell
msbuild WebApplication1.sln /p:Configuration=Release /p:Platform="Any CPU"
```

Chạy tất cả test:

```powershell
vstest.console.exe bin\WebApplication1.dll /Logger:trx /ResultsDirectory:TestResults\Video_All
```

Chạy riêng Unit Tests:

```powershell
vstest.console.exe bin\WebApplication1.dll /TestCaseFilter:"TestCategory!=Integration&TestCategory!=Performance" /Logger:trx /ResultsDirectory:TestResults\Video_Unit
```

Chạy riêng Integration Tests:

```powershell
vstest.console.exe bin\WebApplication1.dll /TestCaseFilter:"TestCategory=Integration" /Logger:trx /ResultsDirectory:TestResults\Video_Integration
```

Chạy riêng Performance Tests:

```powershell
vstest.console.exe bin\WebApplication1.dll /TestCaseFilter:"TestCategory=Performance" /Logger:trx /ResultsDirectory:TestResults\Video_Performance
```

Nói:

> Các lệnh này cũng là ý tưởng mà pipeline CI/CD sẽ tự động chạy trên build agent.

## 7. Cảnh 4 - Tạo GitHub Repository Mới Từ Đầu

Mở trình duyệt và vào GitHub.

Thao tác:

1. Đăng nhập GitHub.
2. Bấm dấu `+` ở góc trên phải.
3. Chọn `New repository`.
4. Repository name: `WebApplication1-CICD`.
5. Description: `ASP.NET MVC automated testing CI/CD demo`.
6. Chọn `Public` hoặc `Private`.
7. Không tick `Add a README file`.
8. Không tick `.gitignore` trên GitHub vì project đã có `.gitignore`.
9. Bấm `Create repository`.

Nói:

> Em tạo mới repository trên GitHub để đưa toàn bộ source code lên và kích hoạt CI/CD.

Sau khi tạo repo, GitHub sẽ hiện URL dạng:

```text
https://github.com/<username>/WebApplication1-CICD.git
```

Copy URL này để dùng ở bước tiếp theo.

## 8. Cảnh 5 - Upload Source Code Lên GitHub Lần Đầu

Mở `Developer PowerShell for VS 2022` tại thư mục project:

```powershell
cd C:\Users\84969\Downloads\SSIS_new\SSIS_new\SSAS_New\WebApplication1
```

Nếu chưa phải Git repo, chạy:

```powershell
git init
```

Đặt tên branch là `main`:

```powershell
git branch -M main
```

Kiểm tra file sẽ upload:

```powershell
git status
```

Thêm file vào commit:

```powershell
git add .
```

Tạo commit đầu tiên:

```powershell
git commit -m "Initial CI/CD automated testing project"
```

Gắn remote GitHub. Thay `<username>` bằng username GitHub của bạn:

```powershell
git remote add origin https://github.com/<username>/WebApplication1-CICD.git
```

Push code lên GitHub:

```powershell
git push -u origin main
```

Nếu Git yêu cầu đăng nhập:

- Chọn đăng nhập bằng browser, hoặc
- Dùng GitHub Personal Access Token nếu Git yêu cầu password.

Nói:

> Sau khi push thành công, source code đã có trên GitHub. Do project có `.gitignore`, các thư mục build như bin, obj, packages và TestResults sẽ không bị upload.

## 9. Cảnh 6 - Kiểm Tra Code Trên GitHub

Trên GitHub repository, quay các điểm:

1. File `WebApplication1.sln`.
2. File `azure-pipelines.yml`.
3. Folder `.github/workflows`.
4. File `.github/workflows/ci-cd.yml`.
5. Folder `Tests`.
6. File `Tests/TestCaseCI_CD.xlsx`.

Nói:

> GitHub hiện đã lưu source code, test code, test case và file cấu hình pipeline.

## 10. Cảnh 7 - Chạy GitHub Actions CI

Sau khi push code lên branch `main`, GitHub Actions sẽ tự động chạy. Workflow cũng đã có `workflow_dispatch`, nên có thể bấm chạy thủ công khi quay video.

Thao tác:

1. Vào tab `Actions`.
2. Chọn workflow `CI/CD Pipeline`.
3. Bấm vào run mới nhất.
4. Quay các step:
   - Checkout source
   - Setup MSBuild
   - Restore NuGet packages
   - Build solution
   - Run unit tests
   - Run integration tests
   - Run performance tests
   - Upload test results
   - Upload build artifact

Nói:

> GitHub Actions là một hệ thống CI/CD tích hợp sẵn trên GitHub. Mỗi lần push code lên main, workflow sẽ tự động build và chạy test.

Nếu workflow chưa tự chạy hoặc muốn quay cảnh bấm nút chạy thủ công:

1. Vào tab `Actions`.
2. Chọn `CI/CD Pipeline`.
3. Bấm `Run workflow`.
4. Chọn branch `main`.
5. Bấm nút `Run workflow` màu xanh.

## 11. Cảnh 8 - Giới Thiệu Azure DevOps Pipeline

Mở file `azure-pipelines.yml`.

Giải thích:

```yaml
trigger:
  - main
  - develop
```

Nói:

> Pipeline sẽ tự động chạy khi có code push vào branch main hoặc develop.

Giải thích các stage:

1. `Build`: restore NuGet, build solution, chạy 70 unit tests.
2. `IntegrationTest`: chạy 10 integration tests.
3. `Performance`: chạy 3 performance tests.
4. `Package`: tạo artifact deploy `WebApplication1-drop`.
5. `Summary`: in tổng kết 83 automated tests.

Nói:

> Đây là pipeline CI/CD chính của project. CI gồm build và test. CD trong video này được thể hiện bằng bước đóng gói artifact sẵn sàng deploy.

## 12. Cảnh 9 - Tạo Azure DevOps Pipeline Từ GitHub Repo

Trên Azure DevOps:

1. Vào project Azure DevOps.
2. Chọn `Pipelines`.
3. Chọn `New pipeline`.
4. Chọn `GitHub`.
5. Đăng nhập/authorize GitHub nếu được hỏi.
6. Chọn repo `WebApplication1-CICD`.
7. Chọn `Existing Azure Pipelines YAML file`.
8. Chọn branch `main`.
9. Chọn path `/azure-pipelines.yml`.
10. Bấm `Continue`.
11. Bấm `Run`.

Nói:

> Azure DevOps sẽ đọc file YAML trong GitHub repository và chạy pipeline theo các stage đã cấu hình.

## 13. Cảnh 10 - Xem Kết Quả Azure Pipeline

Quay màn hình các stage:

- Build
- IntegrationTest
- Performance
- Package
- Summary

Khi pipeline pass, mở:

1. Tab `Tests` để xem test results.
2. Tab `Artifacts` để xem `WebApplication1-drop`.

Nói:

> Kết quả pipeline pass chứng minh quy trình build, kiểm thử tự động và đóng gói artifact đã được tự động hóa.

## 14. Cảnh 11 - Giới Thiệu File TestCaseCI_CD.xlsx

Mở file:

```text
Tests\TestCaseCI_CD.xlsx
```

Giới thiệu:

- Sheet `Summary`: tổng hợp số lượng test.
- Sheet `TestCases`: danh sách test case.

Nói:

> File Excel này dùng để đối chiếu giữa test case và automated test. Hiện có 83 automated tests và 5 dòng kiểm tra pipeline.

Số liệu đúng:

- Automated tests total: 83
- Unit tests: 70
- Integration tests: 10
- Performance tests: 3
- Pipeline validation checks: 5

## 15. Câu Nói Kết Video

Nói:

> Như vậy, project đã có quy trình kiểm thử tự động hóa CI/CD. Source code được quản lý trên GitHub, GitHub Actions có thể build và test tự động, Azure DevOps Pipeline thực hiện build, unit test, integration test, performance test và tạo artifact sẵn sàng deploy.

## 16. Lỗi Hay Gặp Và Cách Sửa

### Lỗi 1: Git báo chưa cấu hình user

Nếu commit bị lỗi, chạy:

```powershell
git config --global user.name "Tên của bạn"
git config --global user.email "emailgithub@example.com"
```

Sau đó commit lại:

```powershell
git commit -m "Initial CI/CD automated testing project"
```

### Lỗi 2: GitHub không cho push bằng password

GitHub không dùng password thường cho Git HTTPS. Hãy đăng nhập bằng browser hoặc dùng Personal Access Token.

### Lỗi 3: Pipeline không tìm thấy test

Kiểm tra trong `azure-pipelines.yml` phải có:

```yaml
testAssembly: '**\bin\WebApplication1.dll'
```

### Lỗi 4: Test Explorer không hiện test

Làm:

1. `Build > Rebuild Solution`.
2. `Test > Test Explorer`.
3. Bấm refresh hoặc `Run All Tests`.

### Lỗi 5: Azure DevOps không thấy GitHub repo

Làm:

1. Vào GitHub authorization của Azure DevOps.
2. Cấp quyền cho repo vừa tạo.
3. Quay lại Azure DevOps và chọn repo lại.

## 17. Checklist Trước Khi Quay

Trước khi bấm record:

- Visual Studio 2022 mở được `WebApplication1.sln`.
- Web chạy được bằng IIS Express.
- Test Explorer hiện 83 tests.
- File `Tests\TestCaseCI_CD.xlsx` mở được.
- Đã tạo GitHub repo mới.
- Đã copy GitHub repo URL.
- Máy đã cài Git.
- Azure DevOps project đã sẵn sàng nếu muốn quay pipeline.

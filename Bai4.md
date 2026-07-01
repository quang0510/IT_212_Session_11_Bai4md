1. Mega-Prompt Duy Nhất
    Đóng vai một Senior QA Automation Engineer kiêm Chuyên gia Bảo mật Hệ thống.

    Nhiệm vụ của bạn là thiết kế một bộ kịch bản kiểm thử toàn diện, sinh dữ liệu giả lập (Mock Data) chuẩn JSON và viết mã nguồn Unit Test bằng JUnit 5 cho phương thức `login(String username, String password)` thuộc lớp `UserAuthentication`.

    Hiện tại dự án chưa cấu hình cơ sở dữ liệu kiểm thử, do đó chúng tôi cần thực hiện Mock dữ liệu trực tiếp. Bạn hãy tự giả định cấu trúc logic của hàm `login` hoạt động dựa trên các quy tắc kiểm tra thông thường để chạy bộ test này.

    Hãy thực hiện tuần tự các yêu cầu sau trong một lượt phản hồi duy nhất:

    YÊU CẦU 1: SINH MOCK DATA (JSON)
    Tạo một mảng JSON chứa đúng 5 đối tượng test case đại diện cho 5 kịch bản sau. Mỗi đối tượng phải có các trường: `testCaseId`, `description`, `inputUsername`, `inputPassword`, và `expectedResult` (kiểu chuỗi hoặc cấu trúc mô tả kết quả mong đợi như ném ra Exception hay trả về true/false):
    1. Kịch bản 1 (Thông tin đúng): Đăng nhập thành công với tài khoản và mật khẩu hợp lệ.
    2. Kịch bản 2 (Sai mật khẩu): Tài khoản có tồn tại nhưng nhập sai mật khẩu.
    3. Kịch bản 3 (Tài khoản bị khóa): Tài khoản hợp lệ nhưng đang trong trạng thái bị khóa (Locked).
    4. Kịch bản 4 (SQL Injection): Tham số `username` hoặc `password` chứa chuỗi mã độc tấn công dữ liệu (ví dụ: `' OR '1'='1`).
    5. Kịch bản 5 (Mật khẩu rỗng): Tham số `password` để trống hoặc rỗng.

    YÊU CẦU 2: SINH MÃ NGUỒN UNIT TEST (JUNIT 5)
    Viết một class kiểm thử Java sử dụng framework JUnit 5 (Jupyter) và thư viện Mockito (nếu cần). 
    - Sử dụng Java 17.
    - Khởi tạo hoặc giả lập lớp `UserAuthentication` để kiểm thử phương thức `login(String username, String password)`.
    - Viết các phương thức `@Test` rõ ràng ứng với từng kịch bản, hoặc sử dụng `@ParameterizedTest` kết hợp `@MethodSource` để đọc và duyệt qua cấu trúc của bộ dữ liệu JSON đã sinh ở Yêu cầu 1.
    - Assert chính xác kết quả trả về hoặc ngoại lệ (Exception) được ném ra đúng theo kỳ vọng của từng kịch bản (ví dụ: `InvalidCredentialsException`, `AccountLockedException`, `IllegalArgumentException`,...).

    Quy định định dạng: Chỉ trả về 1 code block JSON cho bộ dữ liệu và 1 code block Java cho mã nguồn Unit Test. Không viết lời giải thích dông dài ở ngoài các khối code.

2. Kết quả từ AI
    ```json
    [
    {
        "testCaseId": "TC01_VALID_LOGIN",
        "description": "Đăng nhập thành công với tài khoản và mật khẩu hợp lệ.",
        "inputUsername": "techshop_user",
        "inputPassword": "ValidPassword123!",
        "expectedResult": "SUCCESS"
    },
    {
        "testCaseId": "TC02_WRONG_PASSWORD",
        "description": "Tài khoản có tồn tại nhưng nhập sai mật khẩu.",
        "inputUsername": "techshop_user",
        "inputPassword": "WrongPassword999",
        "expectedResult": "THROW_INVALID_CREDENTIALS_EXCEPTION"
    },
    {
        "testCaseId": "TC03_LOCKED_ACCOUNT",
        "description": "Tài khoản hợp lệ nhưng đang trong trạng thái bị khóa (Locked).",
        "inputUsername": "locked_user",
        "inputPassword": "ValidPassword123!",
        "expectedResult": "THROW_ACCOUNT_LOCKED_EXCEPTION"
    },
    {
        "testCaseId": "TC04_SQL_INJECTION",
        "description": "Tham số đầu vào chứa chuỗi mã độc tấn công dữ liệu.",
        "inputUsername": "admin' OR '1'='1",
        "inputPassword": "compromised_pwd",
        "expectedResult": "THROW_SECURITY_EXCEPTION"
    },
    {
        "testCaseId": "TC05_EMPTY_PASSWORD",
        "description": "Tham số password để trống hoặc rỗng.",
        "inputUsername": "techshop_user",
        "inputPassword": "",
        "expectedResult": "THROW_ILLEGAL_ARGUMENT_EXCEPTION"
    }
    ]

    ```

    ```java
    import org.junit.jupiter.api.BeforeEach;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Test;
    import static org.junit.jupiter.api.Assertions.*;

    /**
    * Lớp kiểm thử đơn vị JUnit 5 cho phương thức login thuộc UserAuthentication.
    * Triển khai kiểm thử 5 kịch bản nghiệp vụ và bảo mật dựa trên Java 17.
    */
    class UserAuthenticationTest {

        private UserAuthentication authService;

        // Định nghĩa các ngoại lệ nghiệp vụ giả định theo chuẩn cấu trúc hệ thống
        static class InvalidCredentialsException extends RuntimeException {
            public InvalidCredentialsException(String message) { super(message); }
        }

        static class AccountLockedException extends RuntimeException {
            public AccountLockedException(String message) { super(message); }
        }

        static class SecurityException extends RuntimeException {
            public SecurityException(String message) { super(message); }
        }

        // Lớp giả lập logic nghiệp vụ của UserAuthentication để phục vụ Unit Test
        static class UserAuthentication {
            public boolean login(String username, String password) {
                if (password == null || password.isBlank()) {
                    throw new IllegalArgumentException("Mật khẩu không được để trống.");
                }
                if (username != null && (username.contains("'") || username.contains("OR"))) {
                    throw new SecurityException("Phát hiện nguy cơ tấn công dữ liệu độc hại!");
                }
                if ("locked_user".equals(username)) {
                    throw new AccountLockedException("Tài khoản đã bị khóa.");
                }
                if ("techshop_user".equals(username) && "ValidPassword123!".equals(password)) {
                    return true;
                }
                throw new InvalidCredentialsException("Sai tài khoản hoặc mật khẩu.");
            }
        }

        @BeforeEach
        void setUp() {
            authService = new UserAuthentication();
        }

        @Test
        @DisplayName("TC01: Đăng nhập thành công với thông tin hợp lệ")
        void testLogin_Success() {
            boolean result = authService.login("techshop_user", "ValidPassword123!");
            assertTrue(result, "Đăng nhập phải thành công và trả về true.");
        }

        @Test
        @DisplayName("TC02: Ném ngoại lệ InvalidCredentialsException khi sai mật khẩu")
        void testLogin_WrongPassword() {
            assertThrows(InvalidCredentialsException.class, () -> {
                authService.login("techshop_user", "WrongPassword999");
            }, "Hệ thống phải báo sai thông tin xác thực.");
        }

        @Test
        @DisplayName("TC03: Ném ngoại lệ AccountLockedException khi tài khoản bị khóa")
        void testLogin_AccountLocked() {
            assertThrows(AccountLockedException.class, () -> {
                authService.login("locked_user", "ValidPassword123!");
            }, "Hệ thống phải ngăn chặn đăng nhập đối với tài khoản bị khóa.");
        }

        @Test
        @DisplayName("TC04: Ném ngoại lệ SecurityException khi phát hiện ký tự SQL Injection")
        void testLogin_SqlInjection() {
            assertThrows(SecurityException.class, () -> {
                authService.login("admin' OR '1'='1", "compromised_pwd");
            }, "Hệ thống phải kích hoạt cơ chế phòng thủ bảo mật đầu vào.");
        }

        @Test
        @DisplayName("TC05: Ném ngoại lệ IllegalArgumentException khi mật khẩu rỗng")
        void testLogin_EmptyPassword() {
            assertThrows(IllegalArgumentException.class, () -> {
                authService.login("techshop_user", "");
            }, "Hệ thống phải từ chối xử lý khi dữ liệu đầu vào không hợp lệ.");
        }
    }

    ```
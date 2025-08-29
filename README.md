%% ERD v2: có Users đăng nhập, Patrons (độc giả), Subjects (thể loại)
erDiagram
  SYSTEM_USERS {
    bigint  UserID PK                 "Khóa chính"
    varchar Username  UK              "Tên đăng nhập duy nhất"
    varchar PasswordHash              "Hash (bcrypt/argon2) - tạo ở ứng dụng"
    enum    Role                      "ADMIN | LIBRARIAN"
    tinyint IsActive                  "1=Hoạt động"
    bigint  StaffSystemID FK          "Tham chiếu nhân sự (nếu là thủ thư)"
    datetime LastLogin                "Lần đăng nhập cuối"
  }

  EMPLOYEES {
    bigint  SystemID PK               "Nhân sự thư viện"
    varchar EmployeeID UK             "Mã nhân sự hiển thị"
    varchar Name
    varchar Address
    varchar Phone
    varchar Department
  }

  SUBJECTS {
    smallint SubjectCode PK           "Mã chủ đề (3 số)"
    varchar  SubjectName              "Tên chủ đề/thể loại"
  }

  BOOKS {
    bigint  BookID PK
    varchar ISBN UK                   "NNN-NNNN = SubjectCode-SubjectSeq"
    varchar Title
    varchar Author
    smallint SubjectCode FK           "FK -> SUBJECTS"
    int     SubjectSeq                "STT trong chủ đề"
  }

  BOOK_COPIES {
    bigint  CopyID PK
    bigint  BookID  FK
    int     CopySeq
    varchar CallNumber UK             "XX-XX-NNN (Title-Author-Seq)"
    tinyint IsAvailable
  }

  PATRONS {
    bigint  PatronSysID PK            "Khóa chính nội bộ"
    varchar PatronID  UK              "Mã độc giả hiển thị"
    varchar Name
    varchar Address
    varchar Phone
    varchar Email
    tinyint IsActive
  }

  BORROW_RECORDS {
    bigint  RecordID PK
    bigint  CopyID        FK          "Bản sao mượn"
    bigint  PatronSysID   FK          "Độc giả mượn"
    date    IssueDate
    date    DueDate
    date    ReturnDate
    decimal LateFee
    bigint  CheckoutByUserID FK       "User xử lý mượn"
    bigint  CheckinByUserID  FK       "User xử lý trả (nullable)"
  }

  %% Quan hệ
  EMPLOYEES ||--o{ SYSTEM_USERS   : "belongs-to staff"
  SUBJECTS  ||--o{ BOOKS          : "categorizes"
  BOOKS     ||--o{ BOOK_COPIES    : "has copies"
  PATRONS   ||--o{ BORROW_RECORDS : "borrows"
  BOOK_COPIES ||--o{ BORROW_RECORDS : "is recorded in"
  SYSTEM_USERS ||--o{ BORROW_RECORDS : "operates"

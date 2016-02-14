1. 協議
  * 總是使用HTTPS
2. 域名
  * 盡量將API部署在專用域名之下
  ````
    https://api.example.com
  ````
  * 如果確定API很簡單, 不會有進一步擴展, 可考慮放在主域名之下
  ````
    https://example.com/api/
  ````
3. 版本
  * 應該將API版本放入URL中
  ````
    https://api.example.com/v1/
  ````
4. 路徑
  * 只能有名詞
  * 所用名詞應該與資料庫中的表格名對應
  * 所用名詞應該使用複數
  ````
    https://api.example.com/v1/zoos
    https://api.example.com/v1/animals
    https://api.example.com/v1/employees
  ````
5. HTTP動詞
  * GET(SELECT): 從Server取出資源(一項或多項)
  ```` 
    Get /zoos: 列出所有動物園
    Get /zoos/ID/animals 列出某個指定動物園的所有動物
  ````
  * POST(CREATE): 在Server新建資源
  ````
    POST /zoos: 新建一個動物園
  ````
  * PUT(UPDATE): 在Server更新資源(客戶端提供改變後的完整資源)
  ```` 
    PUT /zoos/ID: 更新某個指定動物園的資訊(提供該動物園的全部資訊)
  ````
  * PATCH(UPDATE): 在server更新資源(客戶端提供改變的屬性)
  ````
    PATCH /zoos/ID: 更新某個指定動物園的資訊(提供該動物園的部分資訊)
  ````
  * DELETE(DELETE): 從Server刪除資源
  ````
    DELETE /zoos/ID: 刪除某個動物園
    DELETE /zoos/ID/animals/ID: 刪除指定動物園的指定動物
  ````
6. 過濾資訊
  * API應該提供參數, 以便過濾返回給客戶端的結果
  ````
    ?limit=10
    ?offset=10
    ?page=2&per_page=100
    ?sortby=name&order=asc
    ?animal_type_id=1
  ````
  * 參數的設計允許存在冗餘, 即允許API路徑和URL參數偶爾有重覆, 例如以下兩者為等價的
  ````
    GET /zoo/ID/animals
    GET /animals?zoo_id=ID
  ````
7. 狀態碼
  * 200 OK - [GET] Server成功返回用戶請求的資料 (**Idempotent**)
  * 201 Created - [POST|PUT|PATCH] 用戶新建或修改資料成功
  * 202 Accepted - [*] 一個非同步請求任務已經進入後台排隊
  * 204 No Content - [DELETE] 用戶刪除資料成功
  * 400 Invalid Request - [POST|PUT|PATCH] 用戶發出請求是有錯誤, 服務器沒有進行新建或修改資料的操作
  * 401 Unathorized - [*] 用戶沒有權限(Token, UserName或Password錯誤)
  * 403 Forbidden - [*] 用戶得到授權, 但該訪問是禁止的(與401錯誤相對)
  * 404 Not Found - [*] 用戶發出的請求是不存在的記錄, 服務器沒有進行操作
  * 406 Not Acceptable - [GET] 用戶請求的格式不可得(比如用戶請求XML格式, 但只有JSON格式)
  * 410 Gone - [GET] 用戶請求的資源被永久刪除, 且不會再得到的
  * 422 Unprocessable Entity - [POST|PUT|PATCH] 當創建一個資源時, 發生一個驗証錯誤
  * 500 Internal Server Error - [*] 服務器發生錯誤, 用戶無法判斷發出的請求是否成功
8. 錯誤處理
  * 如果狀態碼是4XX, 應該向用戶返回出錯資訊. 一般來說, 各回的資訊中將error作為鍵名, 出錯資訊作為鍵值即可
  ````
  { error: "Invalid API key" }
  ````
9. 返回結果
  * 針對不同操作, Server返回的結果應該符合以下規範
  ````
    GET /collection: 返回資源對象的列表(陣列)
    GET /collection/resource: 返回單個資源對象
    POST /collection: 返回新生成的資源對象
    PUT /collection/resource: 返回完整的資源對象
    PATCH /collection/resource: 返回完整的資源對象
    DELETE /collection/resource: 返回一個空文檔
  ````
10. Hypermedia API
  * HATEOAS
  * 在返回結果中提供鏈結, 連向其他API方法, 使用用戶不查文檔, 也知道下一步該做什麼
    ````
    {"link": { "rel": "collection https://www.example.com/zoos", "href": "https://api.example.com/zoos", "title": "List of zoos", "type": "application/vnd.yourformat+json" }}
    ````
    上面代表中, 文檔有一個link屬性, 用戶讀取這個屬性就知道下一步該調用什麼API
    rel表示這個API與當前網址的關係(collection關係, 並給出讓collection的網址)
    href表示該API的路徑, title表示API的標題
    type表示返回類型
  * 可參考[GitHub API](https://api.github.com/)
11. 身份認證
  * 應該使用OAuth 2.0框架

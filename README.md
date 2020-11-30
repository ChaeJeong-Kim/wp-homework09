# HW09 - 주소검색API 예제 업그레이드
------------

## 과제 주제 설명
> 본 과제는 주소검색API 예제에 영문 도로명 주소, 지번정보, 건물명 등 주소와 관련된 더 많은 정보를 보여주도록 코드를 추가하고 
> 페이지네이션 기능을 추가, UI를 개선하여 예제를 업그레이드 한 것입니다.

## 과제 주요 기능
- 주소 검색 기능 : 도로명 주소를 입력 후 버튼 클릭 시 결과가 검색되어 보여집니다.

- 총 검색된 결과 수를 보여주는 기능

- 더보기 버튼을 통해 행정구역코드, 지번정보, 상세 건물명과 같은 추가 정보를 알려주는 기능

- 페이지네이션 기능 : 페이지마다 10개의 결과가 보여집니다. 페이지버튼은 버튼이 8개까지 보여지며 8개 이상일 시 스크롤을 이용하여 다음 버튼들을 볼 수 있습니다.

- 제목을 클릭 시 새로고침되는 기능

## HTML 주요 코드 설명
HTML의 구조는 크게 header태그, form태그, ul태그, nav태그, div태그로 이루어져 있습니다.

### 1. header태그
- 제목이 입력된 h1태그 포함
     ```html
    <h1 id="title">도로명 주소 검색</h1>
     ```
    
### 2. form태그
- 도로명 입력란과 제출 버튼        
    ```html
    <input type="text" placeholder="도로명 입력" class="form-item">
    <button type="submit"></button>
    ```
### 3. ul태그 
- 검색된 주소가 ul태그의 자식 요소로 추가됩니다.
    ```html
    <ul id="address-list"></ul>
    ```

### 4. nav태그
- 검색 결과에서 더보기 메뉴 부분을 나타냅니다. ul태그는 4개의 li태그로 구성되어 있고, 검색 결과의 한 항목마다 아래 두번째 span에 내용이 추가됩니다.
    ```html
    <ul id="hidden-texts">
        <li class="hidden-text">
            <span>도로명 주소</span>
            <span>0</span>
        </li>
        <li class="hidden-text"> ... </li>
        <li class="hidden-text"> ... </li>
        <li class="hidden-text"> ... </li>
    </ul>
    ```

### 5. div태그
- 검색된 결과의 하단에 위치한 페이지네이션 부분입니다. 검색된 결과의 데이터 수에 따라 아래 div태그의 자식요소로 button태그가 추가됩니다.
    ```html
    <div class="pagination" id="pagination"></div>
    ```


## JS 주요 코드 설명

### [form태그에 이벤트 추가]

```js
form.addEventListener('submit', async e => {
                    ...
});   
```
- form태그에 submit이벤트 발생 시 함수를 호출하도록 addEventListener()를 사용함
---

### [submit이벤트 발생 시 호출되는 함수]

```js
async e => { 
    ...
    try {
        const response = await search(keyword, currentPage, countPerPage);
        const txt = await response.text();
        ...
    } catch (e) { ... }
}
```
- async/await를 이용하여 비동기처리를 하였는데 search()함수와 response.text() 앞에 awit 키워드를 붙여 비동기로 처리함 
- try/catch를 이용하여 reject 상태일 시 catch 부분이 실행되도록 예외 처리를 함


```js
try {
    const response = await search(keyword, currentPage, countPerPage);
    const txt = await response.text();
    const results = JSON.parse(txt.replace(/^\(/,'').replace(/\)$/,'')).results;
    const totalItems = results.common.totalCount;   //총 데이터 수

    display(results);
    displayPagination(totalItems, countPerPage, currentPage, results, keyword);    
    form.reset();
} 
```
- keywod와 currentPage, countPerPage를 인수로 하는 search함수를 호출하여 response에 저장함
- response.text()한 값은 txt에 저장되며 이 txt에 대해 양쪽 괄호를 제거한 값은 results에 저장됨
- results를 인수로 하는 display()함수와 5개의 인수를 가진 displayPagination()함수를 호출함
---

### [display() 함수]

function display(results)은 '총 검색 결과 수'를 보여주고 검색된 결과의 각 주소마다 
한글도로명주소, 영문도로명 주소, 더보기 버튼 등에 대한 요소와 내용을 추가하고 보여주는 함수입니다.


```js
const totalCount = document.createElement('div');
totalCount.innerHTML=`* 총 검색 결과 수는 ${results.common.totalCount}개 입니다.`;
```
- div태그를 생성하고 innerHTML을 이용하여 총 검색 결과 수에 대한 내용을 추가함

```js
results.juso.forEach(item => {
    const containSubjects = document.createElement('div');
    const subjects = document.createElement('div'); 
    const subject1 = document.createElement('div'); 
    const sub1Name = document.createElement('span');
    const sub1Text1 = document.createElement('span');
    const sub1Text2 = document.createElement('span');
                
    const subject2 = document.createElement('div'); 
    const sub2Name = document.createElement('span');
    const sub2Text = document.createElement('span');

    const sub1More = document.createElement('button'); 
    const sub1MoreDiv1 = document.createElement('div'); 
    const sub1MoreDiv2 = document.createElement('div');

                  ...
            
    subject1.appendChild(sub1Name);
    subject1.appendChild(sub1Text1);
    subject1.appendChild(sub1Text2);

    subject2.appendChild(sub2Name);
    subject2.appendChild(sub2Text);

    subjects.appendChild(subject1);
    subjects.appendChild(subject2);

    sub1More.appendChild(sub1MoreDiv1);
    sub1More.appendChild(sub1MoreDiv2);

    containSubjects.appendChild(subjects);
    containSubjects.appendChild(sub1More);
    addressList.appendChild(containSubjects);
```
- 위 코드에서 생성되고 붙여진 요소들을 정리하면 다음과 같습니다.

  > addressList 
  >> containSubjects
  >>> subjects
  >>>> subject1
  >>>>> sub1Name, sub1Text1, sub1Text2
  >>>
  >>>> subject2
  >>>>> sub2Name, sub2Text
  >>
  >>> sub1More 
  >>>> sub1MoreDiv1, sub1MoreDiv2

```js
results.juso.forEach(item => {
            ...
    sub1More.addEventListener('click', () => {
        if (hidden) {
            hiddenMenu.classList.remove('hidden');
            hiddenText0.innerHTML = `${item.roadAddr}`; //도로명 주소
            hiddenText1.innerHTML = `${item.admCd}`; //행정구역코드
            hiddenText2.innerHTML = `${item.jibunAddr}`; //번지 주소
                        ...
        } else {
            hiddenMenu.classList.add('hidden');
            hidden = true;
            }
        });
    });
}
```
- 더보기 버튼에 클릭 이벤트를 추가하여 더보기 버튼 클릭 시 더보기 메뉴가 나타나고, 사라지도록 함
- 더보기 버튼을 클릭하여 더보기 메뉴가 나타날 때 더보기 메뉴 요소들의 내용을 도로명 주소, 행정구역 코드, 번지 주소 등으로 바꿈
---

### [displayPagination() 함수]
function displayPagination(...)은 페이지네이션의 페이지 버튼을 추가해주는 함수입니다.
```js
function displayPagination(totalItems, rows, page, results, keyword) {
    pagination.innerHTML='';
    const page_count = Math.ceil(totalItems / rows);
    for (let page = 1; page < page_count + 1; page++) {
        const btn = PaginationButton(page, results, keyword);
        pagination.appendChild(btn); }
}  
```
- '총 데이터 수'를 '페이지마다 보일 데이터의 수'로 나누어 반올림한 값을 page_count에 저장함
- for문을 이용하여 페이지가 1일 때부터 page_count일 때 동안 페이지 버튼을 붙임
---

### [PaginationButton() 함수]
function PaginationButton(page, results, keyword)은 페이지 버튼인 button태그를 생성하고 페이지네이션의 페이지 버튼을 누를 때마다 페이지에 맞게 결과 화면이 바뀌도록 하는 함수 입니다.
```js
function PaginationButton(page, results, keyword) {
    const button = document.createElement('button');
                      ...
    button.addEventListener('click', () => {
             ...
        contentPerPage();     
    });
    return button;
}  
```
- button태그를 생성한 후 이에 이벤트를 추가하여 'click'할 때 contentPerPage()함수가 실행되도록 함

### [contentPerPage() 함수]
function contentPerPage()은 PaginationButton함수 내부에 정의된 함수이고 입력된 키워드를 검색하여 그 결과를 보여주는 함수이다.
```js
async function contentPerPage() {
    try{
        const res = await search(keyword, currentPage, countPerPage);
        const txt = await res.text();
        const result = JSON.parse(txt.replace(/^\(/,'').replace(/\)$/,'')).results
        display(result);
        form.reset();
    } catch (err) {
        console.error(err.message);
    }
}
```
- async/await패턴을 이용하여 비동기처리를 하고 try/catch를 통해 예외처리를 함
- 위에서 form태그에 'submit'이벤트 발생 시 실행되는 코드의 구조와 비슷함 
---

### [title에 새로고침되는 이벤트 추가]
```js
const title = document.getElementById('title');
title.addEventListener('click', () => {
    location.reload();
})
```
- title에 'click'이벤트 발생 시 location.reload()가 실행되도록 함
- location.reload(): 페이지를 새로고침함


## CSS 주요 코드 설명
```css
#form {
    display: flex;
    justify-content: center;
         ...
}
```
- 아이디가 form인 요소를 display:flex를 통해 정렬함
- justify-content:center를 통해 요소가 가로축에 대해 중앙에 위치하도록 함

```css
#form-button {
        ...
    cursor: pointer; 
}
#form-button:hover { background-color: rgb(247, 160, 0); }
```
- #form-button요소에 대해 cursor: pointer로 설정하여 마우스를 올릴 시 마우스 커서가 바뀌도록 함
- #form-button:hover에 대해 배경색을 rgb(247, 160, 0)로 설정하여 마우스를 올릴 시 원래 배경색과 색이 달라지도록 함

```css
#address-list {
    width: 70%;
        ...
}
```
- 검색 결과가 나타나는 곳인 #address-list요소에 대해 너비를 70%로 설정함

```css
.subjects {
    ...
    overflow: auto;
}
```
- 결과화면에서 더보기버튼을 제외하고 주소부분 포함하는 .subjects 요소에 대해 overflow를 auto로 설정하여 내용이 넘칠 때 스크롤을 통해 보이도록 함

```css
.sub1-more:hover { background-color: rgb(133, 133, 133); }
.sub1-more:focus {
    outline: none;
    border:none;
    background-color: rgb(133, 133, 133);
}
```
- .sub1-more은 더보기 버튼으로 마우스를 올릴 때와 눌린 상태일 때 배경색이 rgb(133, 133, 133)로 되도록 함

```css
#hidden-menu {
    position: fixed;
    top: 50%;
    right: 18%;
         ...
    overflow: auto;
    transition-duration: 400ms;
}
```
- hidden-menu는 더보기 버튼을 클릭 시 보여지는 더보기 메뉴로 이 요소를 position:fixed를 통해 위치를 고정하였고 위쪽으로부터 전체화면 비율의 50%, 오른쪽으로부터 18%에 위치하도록함
- 이 요소의 내용이 넘칠 시 스크롤바가 생기도록 하였으며 transition-duration: 400ms를 이용하여 400ms속도로 메뉴가 이동되도록 설정함

```css
#hidden-menu.hidden { right: -100%; }
```
- 더보기 메뉴가 오른쪽으로부터 100% 떨어지게 하여 화면에 보이지 않도록 함 

```css
.pagination {
    display: flex;
       ...
    overflow: auto;
    width: 350px;
    margin: 50px auto;
}
```
- 페이지네이션 부분을 나타내는 .pagination에 대해 flex를 통해 정렬하였고 margin: 50px auto로 설정하여 상하로는 50px씩 공간을 추가하고 가운데 정렬이 되도록 함
- 너비를 350px로 설정하고 너비에 비해 버튼이 많아 보이지 않는 경우에는 스크롤을 통해 버튼이 보이도록 함 

```css
.pagination button:hover { background-color:rgb(43, 32, 187); }

.pagination button:focus {
    background-color: rgb(43, 32, 187);
            ...
}
```
- 페이지네이션에 있는 페이지 버튼에 마우스를 올릴 때와 눌린 상태일 때 배경색이 rgb(43, 32, 187)로 되도록 함


## 비고 및 고찰
> - 평소 api라는 용어를 자주 들어보고 오픈api가 있다는 것도 알았지만 어떻게 활용하는지는 몰랐는데 이번 과제를 통해 자바스크립트에서 api를 어떻게 활용해야 하는지 알 수 있었습니다.
> - 또한 예제를 업그레이드 하는 과정에서 잘 실행되지 않는 부분과 모르는 부분에 대해 검색하고 강의자료를 다시 찾아봄으로써 몰랐던 지식을 새롭게 알게되고 잊어버렸던 지식들을 다시 떠올리는 시간이 되었습니다. 
> - 주소 검색 api 예제를 분석함으로써 콜백함수와 Promise에 대한 이해도가 더 높아졌고 함수가 어떻게 정의되고 호출되는지에 대해서도 확실히 알게 되었습니다.

> - 콜백함수와 Promise, Async/await가 활용된 예제를 볼 때는 이해할 수 있으나 이것들을 직접 활용하려고 할 때는 어려움이 있습니다.
> - 주소 검색 api외에 다른 api를 추가해보려고 시도했지만 실패하였고 이에 대해서는 더 학습할 필요가 있습니다.
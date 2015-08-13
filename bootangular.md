## Bootstrap 

### Tips
- Row는 다음과 같다. 기본적으로 row를 써주는게 margin control에서 도움이 됨
```css
.row {
  margin-right: -15px;
  margin-left: -15px;
}
```

- Responsive web의 원리는 media query
```css
@media (min-width: 1200px) {
  .container {
    width: 1170px;
  }
}
```

- clearfix 활용 :  25$ grid 4개가 반응형에 의해 2개가 되었을 때 높이를 맞추고 싶을때
```
	<div class="row">
		<div class="col-xs-6 col-sm-3">
			<div class="large-box">1</div>
		</div>
		<div class="col-xs-6 col-sm-3">
			<div class="small-box">2</div>
		</div>
		<!-- xs 에서만 block 되는 div 로 fix 한다. -->
		<div class="clearfix visible-xs-block"></div>
		<div class="col-xs-6 col-sm-3">
			<div class="small-box">3</div>
		</div>
		<div class="col-xs-6 col-sm-3">
			<div class="small-box">4</div>
		</div>
	</div>
```

- push와 pull을 이용하여 grid 순서 변경 가능
```
	<h3>Column Ordering (칼럼 정렬)</h3>
	<div class="row">
		<div class="col-sm-9 col-sm-push-3">
			<div class="small-box">앞</div>
		</div>
		<div class="col-sm-3">
			<div class="small-box">뒤</div>
		</div>
	</div>
```

# AngularJS

## CORS
- JSONP 활용

```javascript
    	var url = "http://localhost:3000/jsonpTest?callback=JSON_CALLBACK"
    	
    	$http.jsonp(url, {params: {page:1, name:"해골"}}).success(function (data) {
    		console.log(data);
    	});
```

## Lazy dynamic Controller loading
```javascript
    myApp.config(function($routeProvider, $controllerProvider) {

        $routeProvider
            .when("/home", {
                templateUrl: "/view/home.html"

            })
            .when("/userList", {
                templateUrl: "/view/userList.html",
                controller : "testCtrl"
            })
            .otherwise({
                redirectTo: "/home"
            });

		$controllerProvider.register("testCtrl", function($scope){
			console.log("hahaha");
			$scope.ttest = "dddd"
		})         

    });
```

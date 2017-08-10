# hello-wrld
My first.
<!DOCTYPE html>
<html>

<head>
	<meta charset="UTF-8">
	<title>焦点轮播图</title>
	<style type="text/css">
		/*去除内边距,没有链接下划线*/
		* {
			margin: 0;
			padding: 0;
			text-decoration: none;
		}
		/*让<body有20px的内边距*/
		body {
			padding: 20px;
		}
		/*最外围的div*/
		#container {
			width: 600px;
			height: 400px;
			overflow: hidden;
			position: relative;/*相对定位*/
			margin: 0 auto;
		}
		/*包含所有图片的<div>*/

		#list {
			width: 4200px; /*7张图片的宽: 7*600 */
			height: 400px;
			position: absolute; /*绝对定位*/
			z-index: 1;

		}
		/*所有的图片<img>*/
		#list img {
			float: left;/*浮在左侧*/
		}
		/*包含所有圆点按钮的<div>*/
		#pointsDiv {
			position: absolute;
			height: 10px;
			width: 100px;
			z-index: 2;
			bottom: 20px;
			left: 250px;
		}
		/*所有的圆点<span>*/

		#pointsDiv span {
			cursor: pointer;
			float: left;
			border: 1px solid #fff;
			width: 10px;
			height: 10px;
			border-radius: 50%;
			background: #333;
			margin-right: 5px;
		}
		/*第一个<span>*/

		#pointsDiv .on {
			background: orangered;
		}
		/*切换图标<a>*/

		.arrow {
			cursor: pointer;
			display: none;
			line-height: 39px;
			text-align: center;
			font-size: 36px;
			font-weight: bold;
			width: 40px;
			height: 40px;
			position: absolute;
			z-index: 2;
			top: 180px;
			background-color: RGBA(0, 0, 0, 0.3);
			color: #fff;
		}
		/*鼠标移到切换图标上时*/
		.arrow:hover {
			background-color: RGBA(0, 0, 0, 0.7);
		}
		/*鼠标移到整个div区域时*/
		#container:hover .arrow {
			display: block;/*显示*/
		}
		/*上一个切换图标的左外边距*/
		#prev {
			left: 20px;
		}
		/*下一个切换图标的右外边距*/
		#next {
			right: 20px;
		}
	</style>
</head>

<body>

	<div id="container">
		<div id="list" style="left: -600px;">
			<img src="img/5.jpg" alt="5" />
			<img src="img/1.jpg" alt="1" />
			<img src="img/2.jpg" alt="2" />
			<img src="img/3.jpg" alt="3" />
			<img src="img/4.jpg" alt="4" />
			<img src="img/5.jpg" alt="5" />
			<img src="img/1.jpg" alt="1" />
		</div>
		<div id="pointsDiv">
			<span index="1" class="on"></span>
			<span index="2"></span>
			<span index="3"></span>
			<span index="4"></span>
			<span index="5"></span>
		</div>
		<a href="javascript:;" id="prev" class="arrow">&lt;</a>
		<a href="javascript:;" id="next" class="arrow">&gt;</a>
	</div>

	<script src="./js/jquery.1.10.2.js"></script>
	<script type="text/javascript">
		/*
		 功能说明:
			 1. 点击向右(左)的图标, 平滑切换到下(上)一页
			 2. 无限循环切换
			 3. 每隔3s自动滑动到下一页
			 4. 鼠标进入停止自动翻页, 移出开启自动翻页
			 5. 切换页面时, 下面的圆点也同步更新
			 6. 点击圆点图标切换到对应的页
		 */
		$(function () {
		   //找到父容器
			var $container = $('#container');
			//找到图片容器
			var $list = $('#list');
			//找到两个方向的按钮
			var $prev = $('#prev');
			var $next = $('#next');
			//初始化单张图片宽度
			var PAGE_WIDTH = 600;
			//小圆点集合
			var $points = $('#pointsDiv>span')
			//初始化index
			var index = 0;
			//定义显示图片张数
			var img_count = 5;
			//判断是否在滑动中的标识
			var moving = false;
			$next.click(function () {
			    //去下一页
				nextPage(true);
			});
			$prev.click(function () {
			    //去上一页
				nextPage(false);
			})
			//每隔2s自动滑动到下一页
			//启动定时器 完成每隔2秒调用一次nextPage
			var autoIntervalId = setInterval(function () {
			    nextPage(true)
			},2000);
			//鼠标进入停止自动翻页, 移出开启自动翻页
			$container.hover(function () {
			    clearInterval(autoIntervalId)
			},function () {
				//更新定时器id 并且重启定时器
				autoIntervalId = setInterval(function () {
					nextPage(true)
				},2000)
			})
//			点击圆点图标切换到对应的页
			$points.click(function () {
				//定义目标index
				var targetIndex = $(this).index();
				nextPage(targetIndex)
			})

			function nextPage (next) {
			    //todo 如果moving是true，return
				if (moving){
					return
				}
				//总偏移量
				var offset = 0;
				//判断参数的数据类型
				if(typeof next === 'boolean'){
					//判断next的值 来确定滑动方向
					offset = next ? -PAGE_WIDTH : PAGE_WIDTH;
				}else {
					offset = -(next-index) *PAGE_WIDTH
				}
				//更改标识为滑动中
				moving = true;
				//定义当前left值
				var currLeft = $list.position().left;
				//获取目的地的left值
				var targetLeft = currLeft + offset;
				//定义动画总时长
				var time = 400;
				//定义每帧的时长
				var itemTime = 20;
				//求出每帧的偏移量
				var itemOffset = offset / (time/itemTime);

				var intervalId = setInterval(function () {
					currLeft += itemOffset;
					if(currLeft === targetLeft){
						clearInterval(intervalId);
						if (currLeft === -(img_count+1)*PAGE_WIDTH){
							currLeft = -PAGE_WIDTH
						}else if(currLeft === 0){
							currLeft = -img_count*PAGE_WIDTH
						}
						moving = false;
					}
					$list.css('left',currLeft);
				},itemTime)
				updatePoints(next);
			}
			function updatePoints (next) {
				//判断数据类型
				if (typeof next === 'boolean'){
					//确定目标index
					var targetIndex = index + (next ? 1:-1)
				}else {
					targetIndex = next;
				}

				//给targetIndex添加class .on  给上一次的元素删除 .on
				if (targetIndex < 0){
					//下标的最大值为长度-1
					targetIndex = img_count-1
				}else if (targetIndex > img_count-1){
					targetIndex = 0;
				}

				$points.eq(targetIndex).addClass('on');
				$points.eq(index).removeClass('on');
				//更新index
				index = targetIndex

			}
		})

	</script>
</body>

</html>

# js
<html>
	<head>
		<title>计算器</title>
	</head>
	<style>
		#main{
			width:190px;
			height:230px;
			background:#cccccc;
			border:1px solid gray;
			padding:10px;
			border-radius:10px;
		}
		#main:hover{
			cursor:pointer;
		}

		#outDiv{
			width:170px;
			height:40px;
			background:white;
			border:1px solid gray;
			line-height:38px;
			overflow:hidden;
		}

		#numDiv{
			width:170px;
			height:170px;
			padding:5px;
		}

		#numDiv div{
			width:30px;
			height:30px;
			margin:5px;
			border:1px solid gray;
			background:#dddddd;
			line-height:30px;
			float:left;
		}

		#numDiv .active{
			background:white;
			color:#ff33ff;
		}

		#numDiv .mout{
			background:#dddddd;
			color:black;
		}

		#numDiv .mclk{
			background:black;
			color:white;
		}
	</style>
	<script>
		window.onload = function(){
			var outDiv = document.getElementById('outDiv');
			var numDiv = document.getElementById('numDiv');
			var aDiv = numDiv.getElementsByTagName('div');
			var first = '';     //第一运算数
			var second = '';		//第二运算数
			var firstEnd = false;    //第一运算数编辑结束状态，false：正在输入第一个运算数  true：第一个运算数输入完成了
			var secondEnd = false;  //第二运算数编辑结束状态，false：第二个运算数的输入未完成  true：第二个运算数输入完成。
			var secondBegin = false;  //第二运算数编辑开始状态。false：不允许编辑第二个运算数  true：允输编辑第二个运算数

			var op = 0;    //用于保存用户点击的运算符  未定为0  加1  减2  乘3  除4

			for(var i=0;i<aDiv.length;i++){    //为每个div自定义一个index属性，注意index并不等于按钮上的数字，而且
				aDiv[i].index = i+1;			//按钮包含+-*/c=等
			}
			
			for(var i=0;i<aDiv.length;i++){     //指谁谁变白
				aDiv[i].onmouseover = function(){
					init();
					this.className = 'active';
				}
			}



			for(var i=0;i<aDiv.length;i++){     //各按钮的单击动作
				aDiv[i].onclick = function(){
					init();
					this.className = 'mclk';    //单击谁谁变黑
					
					var num;        //num用来保存用户当前点击了哪个数字
					if(this.index%4!=0){     //当用户定的是  数字键 以及 c  =  的时候
						if(this.index<4){num=this.index+6;}     //根据index计算用户定了什么数字，把这个数字保存在num上
						else if(this.index<8){num=this.index-1;}
						else if(this.index<12){num = this.index-8;}
						else if(this.index==13){num = 0;}
						else if(this.index==14){num = '';}    //如果点击的是c时，num为空
						else if(this.index == 15){num='';}    //如果点击的是=时，num为空

						if(!firstEnd){					//如果第一操作数输入未结束。
							first = first+num;					//则把num连接到第一个数后，注意first是个字符串;
							outDiv.innerHTML = first;			//在outDiv上显示第一个数
						} else if(secondBegin){			//否则，如昧第一操作数输入完成了，而且第二个操作数允许编辑
							second = second +num;				//则将num连接到第二个数后,注意second也是一个字符串
							outDiv.innerHTML = second;			 //在outDiv上显示第二个数
						}

					} else {    //当用户点击的是 + - * /键时

						if(firstEnd==false && first.length>0){    //如果当前正在编辑第一个数，则现在第一个数的长度不是0
							firstEnd = true;							//第一个数编辑结束
							secondBegin = true;							//第二个数开始接收输入
						} else if(firstEnd==true && second.length>0){//否则，如果第一个数编辑已结束 ，而且第二个数的长度也不是0了
							secondEnd = true;							//此时表示两个数都编辑结束了，第二个数编辑结束
							secondBegin = false;						//第二个数不允许接收输入
						}

						if(firstEnd && secondEnd){   //如果第一第二个数都编辑结束，用户还在点 +-*/  这是要连加连减连乘连除了
							opreate();                      //那就调用计算功能方法opreate();
						}   


						/* --------以下是正常情况，用户点运算符，还不需要计算时 ----------*/
						//用户点了什么运算符？  保存在op中
						if(this.index ==4){op = 4;}       //除
						else if(this.index == 8){op = 3;}   //乘
						else if(this.index == 12){op = 2;}   //减
						else if(this.index == 16){op = 1;}   //加
						secondBegin = true;    //第二个数开始接受编辑。
						
						

					}


					if(this.index == 15){          //如果用户点击了  =  ,要计算了，调用opreate();
						opreate();
						secondBegin = false;
					}
					
					if(this.index == 14){           //如果用户点击了  c，要清除了，调用clear();
						clear();
					}
				}
			}
	
			function init(){                        //全体变灰色
				for(var i=0;i<aDiv.length;i++){
					aDiv[i].className = 'mout';
				}
			}

			function opreate(){                //运算功能
				var x = parseInt(first);       //把first转整数
				if(isNaN(x))x=0;               //如果是空的，就直接置0
				var y = parseInt(second);      //把second转整数
				if(isNaN(y))y=0;				//如果是空的，也直接置0
				var end;                       //end 用来保存计算结果
				if(op == 0) return;			  //如果用户没有点击任何运算符，就点=，这是恶作剧，直接return掉。
				
				if(op==1){end = x+y;}        //如果op==1，也就是加法  ，end = x+y
				else if(op==2){end = x-y;}	//否则，如果是减法，end = x-y;
				else if(op==3){end = x*y;}	//否则，如果是乘法，end = x*y;
				else if(op==4){				//否则，如果是除法
					if(y==0){						//除数为0？  不是恶作剧就是失误乱点
						alert('除数为0，错误！');		//弹一个提示恶心一下
						clear();						//简单粗暴清除所有
						return;							//直接return掉
					} else {						//否则，除数不是0
						end = x/y;						//那就 end = x除以y
					}
					
				}
				outDiv.innerHTML = end;     //将计算结果显示出来

				/*--------下面的代码至关重要-----------*/
				/*  思路：                             */
						/* 1.把计算结果放在第一运算数first中保存起来，当然，first依然是字符串    */
						/* 2.first保存结果后，也不能再编辑了，firstEnd置true    */
						/* 3.第二运算数second要清空了。  */
						/* 4.第二运算数结束编辑状态secondEnd置false,    */
						/* 5.第二运算数开始编辑状态secondBegin置为true    */
						/* 6.表示用户再要输入，则输入的是第二个数了*/

				first = ''+end;
				firstEnd = true;
				second = '';
				secondEnd = false;
				secondBegin = true;
			}


			function clear(){        //清除的代码， 就是把所有的状态全部还原
				outDiv.innerHTML = '';
				first = '';
				second = '';
				op = 0;
				firstEnd = false;
				secondEnd = false;
				secondBegin = false;

			}
		}
	</script>


	<body>
		<center>
			<div id="main">
				<div id="outDiv">
				</div>
				<div id="numDiv">
					<div>7</div>
					<div>8</div>
					<div>9</div>
					<div>/</div>
					<div>4</div>
					<div>5</div>
					<div>6</div>
					<div>*</div>
					<div>1</div>
					<div>2</div>
					<div>3</div>
					<div>-</div>
					<div>0</div>
					<div>c</div>
					<div>=</div>
					<div>+</div>
				</div>
			</div>

		</center>
	</body>
</html>




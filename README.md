# pxb7.com-get-prices-and-statistics
针对于pxb7.com的商品地址、价格、性价比获取。  
[ 中文 | [English](https://github.com/reuAC/pxb7.com-get-prices-and-statistics/blob/re_uAC/README_EN.md) | [日本語](https://github.com/reuAC/pxb7.com-get-prices-and-statistics/blob/re_uAC/README_JP.md) ]

## 介绍
该脚本会遍历当前页面的所有商品，并将关键内容以组为单位保存。  
脚本会检测是否存在X黄的格式，即为商品内说明有多少个最稀有内容，若未匹配到，则不会计入。（您可以根据需要修改代码，或以reuac@qq.com与我取得联系）

## 使用方法
1. 打开pxb7.com并选择一个游戏（可以进行筛选操作）。
2. 打开浏览器开发者工具，复制“获取商品信息”代码到控制台并回车以执行，随后商品信息会被一齐打印出来。
3. 如需导出到Excel或其他软件进行统计，可以复制“导出为csv”代码，并回车以执行，随后浏览器会自动下载一个csv。

**可以在执行一次“获取商品信息”后，翻到下一页后继续执行，前一页的数据会被保留。**

## 获取商品信息
```javascript
const i = document.querySelectorAll(".game_list_details_box")
if (typeof dataArray === 'undefined') {
    var dataArray = [];
}
i.forEach((item)=>{
	var innertext = item.querySelector('span[style="vertical-align: super; margin-left: 0px;"]').innerText;
	var match = innertext.match(/(\d+)黄/);
	
	var target = item.querySelector('a.details_img').getAttribute('href');
	var price = item.querySelector('.details_price').innerText;
	price = price.slice(1);
	
	if (match && match[1]) {
        let number = match[1];
		
		var xingjiabi = Number(price) / Number(number);
		
        dataArray.push({
                数量: Number(number),
                地址: target,
                价格: Number(price),
                性价比: xingjiabi.toFixed(2)
            });
    } else {
        console.log("未找到 'X黄' 的匹配");
    }
})
console.log(dataArray)
```

## 导出为csv
**需要在执行“获取商品信息”后使用。**
```javascript
function exportToCSV(dataArray, filename = 'export.csv') {

    let headers = Object.keys(dataArray[0]).join(',') + '\n';

    let csvContent = headers + dataArray.map(item => {
        return Object.values(item).join(',');
    }).join('\n');

    let bom = '\uFEFF';
    let fullContent = bom + csvContent;

    let blob = new Blob([fullContent], { type: 'text/csv;charset=utf-8;' });

    let link = document.createElement("a");
    if (link.download !== undefined) {
        let url = URL.createObjectURL(blob);
        link.setAttribute("href", url);
        link.setAttribute("download", filename);
        link.style.visibility = 'hidden';
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }
}
exportToCSV(dataArray);
```

**csv默认以UTF-8编码。**

## 输出性价比最低的账户。
**需要在执行“获取商品信息”后使用。**
```javascript
if (dataArray.length > 0) {

    let lowestXingjiabiItem = dataArray.reduce((prev, current) => {
        return Number(prev.性价比) < Number(current.性价比) ? prev : current;
    });

    console.log("性价比最低的一项:", lowestXingjiabiItem);
} else {
    console.log("dataArray 为空，无法计算");
}
```
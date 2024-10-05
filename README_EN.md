# pxb7.com-get-prices-and-statistics
To retrieve product addresses, prices, and cost-effectiveness from pxb7.com.
[ [中文](https://github.com/reuAC/pxb7.com-get-prices-and-statistics/blob/re_uAC/README.md) | English | [日本語](https://github.com/reuAC/pxb7.com-get-prices-and-statistics/blob/re_uAC/README_JP.md) ]

## Introduction
The script will iterate through all the products on the current page and save key content in groups.
The script will check for the format of 'X黄', which indicates how many rare contents are present in the product description. If no match is found, it will not be counted. (You can modify the code as needed, or contact me at reuac@qq.com.)

## Usage
1. Open pxb7.com and select a game (you can use the filtering options).
2. Open the browser's developer tools, copy the "Get Product Information" code into the console, and press Enter to execute it. The product information will then be printed all at once.
3. To export to Excel or other software for analysis, you can copy the "Export as CSV" code and press Enter to execute it. The browser will automatically download a CSV file.

**After executing "Get Product Information" once, you can navigate to the next page and continue executing it; the data from the previous page will be retained.**

## Get Product Information
```javascript
const i = document.querySelectorAll(".game_list_details_box");
if (typeof dataArray === 'undefined') {
    var dataArray = [];
}
i.forEach((item) => {
    var innerText = item.querySelector('span[style="vertical-align: super; margin-left: 0px;"]').innerText;
    var match = innerText.match(/(\d+)黄/);
    
    var target = item.querySelector('a.details_img').getAttribute('href');
    var price = item.querySelector('.details_price').innerText;
    price = price.slice(1);
    
    if (match && match[1]) {
        let number = match[1];
        
        var valueForMoney = Number(price) / Number(number);
        
        dataArray.push({
            quantity: Number(number),
            address: target,
            price: Number(price),
            valueForMoney: valueForMoney.toFixed(2)
        });
    } else {
        console.log("No match found for 'X黄'");
    }
});
console.log(dataArray);
```

## Export as CSV
**You need to use it after executing "Get Product Information."**
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

## Output the account with the lowest cost-effectiveness.
**You need to use it after executing "Get Product Information."**
```javascript
if (dataArray.length > 0) {

    let lowestXingjiabiItem = dataArray.reduce((prev, current) => {
        return Number(prev.valueForMoney) < Number(current.valueForMoney) ? prev : current;
    });

    console.log("The item with the lowest valueForMoney:", lowestXingjiabiItem);
} else {
    console.log("dataArray is empty, cannot calculate");
}
```
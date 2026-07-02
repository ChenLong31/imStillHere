<template>
	<view class="page" :class="{ 'page-exit': isExiting }" :style="themeVars">
		<!-- 水面背景动态光效 -->
		<view class="water-bg">
			<view class="light-spot spot-1"></view>
			<view class="light-spot spot-2"></view>
		</view>

		<!-- 顶部 -->
		<view class="header">
			<text class="greeting">{{ greetingText }}</text>
			<text class="date">{{ currentDate }}</text>
		</view>

		<!-- 中央水滴按钮 -->
		<view class="btn-wrapper">
			<view class="drop-container" @tap="handleSignIn">
				<!-- 同心涟漪环 -->
				<view class="ring" v-for="(r, i) in rings" :key="i" :style="r.style"></view>
				<!-- 按钮主体 -->
				<view class="drop-btn" :class="{ clicking: isClicking }">
					<view class="drop-highlight"></view>
					<text class="btn-text">Still</text>
				</view>
			</view>
		</view>

		<!-- 底部状态 -->
		<view class="status-bar">
			<text class="status-text">{{ statusText }}</text>
		</view>

		<!-- 主题测试切换器 -->
		<view class="theme-switcher">
			<view class="switcher-inner">
				<text class="switcher-label">{{ themeList[currentTheme].label }}</text>
				<view class="switcher-dots">
					<view class="dot" v-for="(_, i) in 4" :key="i"
						:class="{ active: currentTheme === i }"
						@tap="switchTheme(i)"></view>
				</view>
				<view class="switcher-arrows">
					<view class="arrow" @tap="switchTheme((currentTheme + 3) % 4)">‹</view>
					<view class="arrow" @tap="switchTheme((currentTheme + 1) % 4)">›</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script setup lang="uts">
import { ref, computed } from 'vue'

const themeList = [
	{
		label: '清晨',
		css: {
			'--bg': 'linear-gradient(170deg, #eae6f2 0%, #ddd4ea 50%, #d0c4e0 100%)',
			'--spot-1': 'radial-gradient(circle, #d4c8e4, transparent)',
			'--spot-2': 'radial-gradient(circle, #e4d4dc, transparent)',
			'--btn-bg': 'linear-gradient(145deg, #d4a8b8, #c694a8)',
			'--shadow': 'rgba(198,148,168,0.25)',
			'--shadow2': 'rgba(212,168,184,0.14)',
			'--text': 'rgba(50,45,60,0.85)',
			'--sub': 'rgba(50,45,60,0.45)',
			'--ring-s': 'rgba(198,148,168,0.45)',
			'--ring-e': 'rgba(212,168,184,0.12)'
		},
		greeting: '早安，新的一天'
	},
	{
		label: '正午',
		css: {
			'--bg': 'linear-gradient(170deg, #4facfe 0%, #00f2fe 50%, #43e97b 100%)',
			'--spot-1': 'radial-gradient(circle, #4facfe, transparent)',
			'--spot-2': 'radial-gradient(circle, #43e97b, transparent)',
			'--btn-bg': 'linear-gradient(145deg, #00b4db, #0083b0)',
			'--shadow': 'rgba(0,180,219,0.30)',
			'--shadow2': 'rgba(0,131,176,0.15)',
			'--text': 'rgba(255,255,255,0.95)',
			'--sub': 'rgba(255,255,255,0.50)',
			'--ring-s': 'rgba(255,255,255,0.6)',
			'--ring-e': 'rgba(67,233,123,0.15)'
		},
		greeting: '午安，小憩一下'
	},
	{
		label: '午后',
		css: {
			'--bg': 'linear-gradient(170deg, #f2e0c8 0%, #e8d0b0 50%, #dfc4a0 100%)',
			'--spot-1': 'radial-gradient(circle, #e8c8a5, transparent)',
			'--spot-2': 'radial-gradient(circle, #dcc09a, transparent)',
			'--btn-bg': 'linear-gradient(145deg, #c99879, #d4a884)',
			'--shadow': 'rgba(200,150,120,0.28)',
			'--shadow2': 'rgba(212,168,132,0.14)',
			'--text': 'rgba(70,50,35,0.85)',
			'--sub': 'rgba(70,50,35,0.45)',
			'--ring-s': 'rgba(200,150,120,0.45)',
			'--ring-e': 'rgba(212,168,132,0.12)'
		},
		greeting: '下午好，继续加油'
	},
	{
		label: '夜晚',
		css: {
			'--bg': 'linear-gradient(170deg, #0b0a1a 0%, #1a1a3e 40%, #2d2a5e 70%, #1e1b3f 100%)',
			'--spot-1': 'radial-gradient(circle, #667eea, transparent)',
			'--spot-2': 'radial-gradient(circle, #764ba2, transparent)',
			'--btn-bg': 'linear-gradient(145deg, #667eea, #764ba2)',
			'--shadow': 'rgba(102,126,234,0.25)',
			'--shadow2': 'rgba(118,75,162,0.15)',
			'--text': 'rgba(255,255,255,0.9)',
			'--sub': 'rgba(255,255,255,0.45)',
			'--ring-s': 'rgba(255,255,255,0.5)',
			'--ring-e': 'rgba(102,126,234,0.15)'
		},
		greeting: '晚安，回顾今天'
	}
]

// 根据小时自动选择主题
const autoThemeIndex = computed(() => {
	const h = new Date().getHours()
	if (h >= 6 && h < 12) return 0
	if (h >= 12 && h < 14) return 1
	if (h >= 14 && h < 18) return 2
	return 3
})

const currentTheme = ref(autoThemeIndex.value)
const isClicking = ref(false)
const isExiting = ref(false)
const rings = ref<Ring[]>([])

interface Ring {
	id: number
	style: Record<string, string>
}

let ringId = 0

const themeVars = computed(() => themeList[currentTheme.value].css)

const weekDays = ['日', '一', '二', '三', '四', '五', '六']

const currentDate = computed(() => {
	const d = new Date()
	return `${d.getFullYear()}年${d.getMonth() + 1}月${d.getDate()}日 星期${weekDays[d.getDay()]}`
})

const greetingText = computed(() => themeList[currentTheme.value].greeting)

const statusText = '滴——记录今天的坚持'

function switchTheme(index: number) {
	currentTheme.value = index
}

function handleSignIn() {
	console.log('handleSignIn called')
	if (isExiting.value) return
	isExiting.value = true

	uni.showToast({
		title: '跳转中',
		icon: 'none',
		duration: 500
	})

	setTimeout(() => {
		console.log('calling navigateTo')
		uni.navigateTo({
			url: '/pages/mood/mood'
		})
	}, 300)
}
</script>

<style>
	.page {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		min-height: 100vh;
		background: var(--bg);
		padding: 40px 20px;
		box-sizing: border-box;
		overflow: hidden;
		position: relative;
		transition: background 0.8s ease;
	}

	.water-bg {
		position: fixed;
		inset: 0;
		pointer-events: none;
		overflow: hidden;
	}

	.light-spot {
		position: absolute;
		border-radius: 50%;
		filter: blur(60px);
		opacity: 0.18;
		animation: drift 8s ease-in-out infinite alternate;
		transition: background 0.8s ease;
	}

	.spot-1 {
		width: 500px;
		height: 500px;
		background: var(--spot-1);
		top: -100px;
		left: -150px;
	}

	.spot-2 {
		width: 400px;
		height: 400px;
		background: var(--spot-2);
		bottom: -100px;
		right: -120px;
		animation-delay: -4s;
	}

	@keyframes drift {
		0% { transform: translate(0, 0) scale(1); }
		100% { transform: translate(60px, 40px) scale(1.2); }
	}

	.header {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-bottom: 50px;
		position: relative;
		z-index: 1;
	}

	.greeting {
		font-size: 24px;
		font-weight: 600;
		color: var(--text);
		margin-bottom: 6px;
		letter-spacing: 1px;
		transition: color 0.6s ease;
	}

	.date {
		font-size: 14px;
		color: var(--sub);
		letter-spacing: 1px;
		transition: color 0.6s ease;
	}

	.btn-wrapper {
		display: flex;
		align-items: center;
		justify-content: center;
		flex: 1;
		position: relative;
		z-index: 1;
	}

	.drop-container {
		position: relative;
		display: flex;
		align-items: center;
		justify-content: center;
		width: 340px;
		height: 340px;
	}

	.ring {
		position: absolute;
		width: 220px;
		height: 220px;
		border-radius: 50%;
		border-style: solid;
		border-color: var(--ring-s);
		left: 50%;
		top: 50%;
		margin-left: -110px;
		margin-top: -110px;
		transform: scale(0);
		opacity: 0;
		pointer-events: none;
		z-index: 0;
	}

	@keyframes water-ring {
		0% {
			transform: scale(0.5);
			opacity: 0.7;
			border-color: var(--ring-s);
		}
		30% {
			opacity: 0.5;
		}
		100% {
			transform: scale(1.5);
			opacity: 0;
			border-color: var(--ring-e);
		}
	}

	.drop-btn {
		width: 210px;
		height: 210px;
		border-radius: 50%;
		background: var(--btn-bg);
		display: flex;
		align-items: center;
		justify-content: center;
		position: relative;
		z-index: 1;
		cursor: pointer;
		user-select: none;
		-webkit-tap-highlight-color: transparent;
		box-shadow:
			0 0 30px var(--shadow),
			0 10px 40px rgba(0, 0, 0, 0.25),
			inset 0 -6px 12px rgba(0, 0, 0, 0.12),
			inset 0 6px 12px rgba(255, 255, 255, 0.12),
			0 0 2px rgba(255, 255, 255, 0.08);
		transition:
			background 0.6s ease,
			box-shadow 0.6s ease,
			transform 1s cubic-bezier(0.34, 1.56, 0.64, 1);
		overflow: hidden;
	}

	.drop-btn.clicking {
		transform: scale(0.92) translateY(6px);
		box-shadow:
			0 0 20px var(--shadow),
			0 4px 15px rgba(0, 0, 0, 0.35),
			inset 0 2px 6px rgba(0, 0, 0, 0.2),
			inset 0 8px 16px rgba(255, 255, 255, 0.15);
	}

	.drop-highlight {
		position: absolute;
		top: 18%;
		left: 22%;
		width: 40%;
		height: 25%;
		border-radius: 50%;
		background: radial-gradient(
			ellipse at center,
			rgba(255, 255, 255, 0.22) 0%,
			rgba(255, 255, 255, 0.08) 50%,
			transparent 100%
		);
		transform: rotate(-20deg);
		pointer-events: none;
	}

	.btn-text {
		font-size: 36px;
		font-weight: 700;
		color: #fff;
		letter-spacing: 6px;
		text-shadow: 0 2px 8px rgba(0, 0, 0, 0.25);
		position: relative;
		z-index: 2;
	}

	.status-bar {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-top: 50px;
		margin-bottom: 30px;
		position: relative;
		z-index: 1;
	}

	.status-text {
		font-size: 15px;
		color: var(--sub);
		letter-spacing: 1px;
		transition: color 0.6s ease;
	}

	/* ===== 测试切换器 ===== */
	.theme-switcher {
		position: fixed;
		bottom: 20px;
		left: 50%;
		transform: translateX(-50%);
		z-index: 100;
		background: rgba(0, 0, 0, 0.35);
		backdrop-filter: blur(12px);
		-webkit-backdrop-filter: blur(12px);
		border-radius: 40px;
		padding: 6px 16px;
		border: 1px solid rgba(255, 255, 255, 0.12);
	}

	.switcher-inner {
		display: flex;
		align-items: center;
		gap: 12px;
	}

	.switcher-label {
		font-size: 12px;
		color: rgba(255, 255, 255, 0.7);
		font-weight: 500;
		letter-spacing: 1px;
		width: 32px;
		text-align: center;
	}

	.switcher-dots {
		display: flex;
		gap: 8px;
	}

	.dot {
		width: 10px;
		height: 10px;
		border-radius: 50%;
		background: rgba(255, 255, 255, 0.25);
		cursor: pointer;
		transition: all 0.3s ease;
	}

	.dot.active {
		background: #fff;
		transform: scale(1.3);
		box-shadow: 0 0 8px rgba(255, 255, 255, 0.5);
	}

	.switcher-arrows {
		display: flex;
		gap: 4px;
	}

	.arrow {
		width: 26px;
		height: 26px;
		border-radius: 50%;
		background: rgba(255, 255, 255, 0.12);
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 18px;
		color: rgba(255, 255, 255, 0.7);
		cursor: pointer;
		transition: all 0.2s ease;
		line-height: 1;
		padding-bottom: 2px;
	}

	.arrow:active {
		background: rgba(255, 255, 255, 0.25);
		transform: scale(0.9);
	}

	/* ===== 页面退出动画：缩小消失 ===== */
	.page-exit {
		animation: page-zoom-out 0.4s ease-in forwards;
		pointer-events: none;
	}

	@keyframes page-zoom-out {
		0% {
			transform: scale(1);
			opacity: 1;
		}
		100% {
			transform: scale(0.3);
			opacity: 0;
		}
	}
</style>

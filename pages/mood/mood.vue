<template>
	<view class="page page-enter">
		<!-- 柔光背景 -->
		<view class="glow-bg">
			<view class="glow-spot glow-1"></view>
			<view class="glow-spot glow-2"></view>
		</view>

		<!-- 内容 -->
		<view class="content">
			<view class="title-area">
				<text class="title-main">今天的心情是...</text>
				<text class="title-sub">选择一个最能代表你的心情</text>
			</view>

			<view class="mood-grid">
				<view class="mood-card" v-for="(m, i) in moods" :key="i"
					:class="{ selected: selectedIndex === i }"
					@tap="selectMood(i)">
					<view class="mood-emoji">{{ m.emoji }}</view>
					<text class="mood-label">{{ m.label }}</text>
				</view>
			</view>

			<view class="action-bar">
				<view class="btn-confirm" :class="{ active: selectedIndex >= 0 }"
					@tap="handleConfirm">
					<text class="btn-confirm-text">{{ selectedIndex >= 0 ? '记录这一刻' : '请选择一个心情' }}</text>
				</view>
				<view class="btn-skip" @tap="handleSkip">
					<text class="btn-skip-text">跳过</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script setup lang="uts">
import { ref } from 'vue'

interface Mood {
	emoji: string
	label: string
}

const moods: Mood[] = [
	{ emoji: '😊', label: '开心' },
	{ emoji: '😐', label: '平静' },
	{ emoji: '😢', label: '难过' },
	{ emoji: '😤', label: '生气' },
	{ emoji: '🥱', label: '疲惫' },
	{ emoji: '🥰', label: '感恩' },
	{ emoji: '🤔', label: '思考' },
	{ emoji: '🧘', label: '放松' }
]

const selectedIndex = ref(-1)

function selectMood(index: number) {
	selectedIndex.value = index
}

function handleConfirm() {
	if (selectedIndex.value < 0) return

	const mood = moods[selectedIndex.value]
	uni.navigateTo({
		url: '/pages/calendar/calendar?emoji=' + encodeURIComponent(mood.emoji) + '&label=' + encodeURIComponent(mood.label)
	})
}

function handleSkip() {
	uni.navigateTo({
		url: '/pages/calendar/calendar'
	})
}
</script>

<style>
	.page {
		display: flex;
		flex-direction: column;
		align-items: center;
		min-height: 100vh;
		background: linear-gradient(170deg, #0f0c29 0%, #302b63 50%, #24243e 100%);
		padding: 50px 20px 40px;
		box-sizing: border-box;
		position: relative;
		overflow: hidden;
	}

	/* 缩放入场动画 */
	.page-enter {
		animation: page-zoom-in 0.45s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
		transform-origin: center center;
	}

	@keyframes page-zoom-in {
		0% {
			transform: scale(0.3);
			opacity: 0;
		}
		100% {
			transform: scale(1);
			opacity: 1;
		}
	}

	/* ---- 背景光晕 ---- */
	.glow-bg {
		position: fixed;
		inset: 0;
		pointer-events: none;
		overflow: hidden;
	}

	.glow-spot {
		position: absolute;
		border-radius: 50%;
		filter: blur(80px);
		opacity: 0.12;
	}

	.glow-1 {
		width: 400px;
		height: 400px;
		background: radial-gradient(circle, #667eea, transparent);
		top: -80px;
		right: -100px;
		animation: glow-drift 6s ease-in-out infinite alternate;
	}

	.glow-2 {
		width: 350px;
		height: 350px;
		background: radial-gradient(circle, #764ba2, transparent);
		bottom: -60px;
		left: -80px;
		animation: glow-drift 8s ease-in-out infinite alternate-reverse;
	}

	@keyframes glow-drift {
		0% { transform: translate(0, 0); }
		100% { transform: translate(30px, 20px); }
	}

	/* ---- 内容 ---- */
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 100%;
		max-width: 400px;
		position: relative;
		z-index: 1;
		flex: 1;
	}

	/* ---- 标题 ---- */
	.title-area {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-bottom: 40px;
	}

	.title-main {
		font-size: 28px;
		font-weight: 700;
		color: rgba(255, 255, 255, 0.92);
		margin-bottom: 8px;
		letter-spacing: 2px;
	}

	.title-sub {
		font-size: 14px;
		color: rgba(255, 255, 255, 0.45);
		letter-spacing: 1px;
	}

	/* ---- 心情网格 ---- */
	.mood-grid {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		gap: 16px;
		width: 100%;
	}

	.mood-card {
		width: 80px;
		height: 100px;
		border-radius: 20px;
		background: rgba(255, 255, 255, 0.06);
		border: 1.5px solid rgba(255, 255, 255, 0.08);
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 6px;
		cursor: pointer;
		transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
		user-select: none;
		-webkit-tap-highlight-color: transparent;
		backdrop-filter: blur(6px);
		-webkit-backdrop-filter: blur(6px);
	}

	.mood-card:active {
		transform: scale(0.92);
	}

	.mood-card.selected {
		background: rgba(102, 126, 234, 0.25);
		border-color: rgba(102, 126, 234, 0.5);
		transform: scale(1.08);
		box-shadow: 0 0 30px rgba(102, 126, 234, 0.25);
	}

	.mood-emoji {
		font-size: 34px;
		line-height: 1;
	}

	.mood-label {
		font-size: 13px;
		color: rgba(255, 255, 255, 0.7);
		font-weight: 500;
	}

	.mood-card.selected .mood-label {
		color: rgba(255, 255, 255, 0.95);
	}

	/* ---- 操作按钮 ---- */
	.action-bar {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 8px;
		margin-top: auto;
		padding-top: 30px;
	}

	.btn-confirm {
		width: 240px;
		height: 48px;
		border-radius: 40px;
		background: rgba(255, 255, 255, 0.08);
		border: 1px solid rgba(255, 255, 255, 0.1);
		display: flex;
		align-items: center;
		justify-content: center;
		cursor: pointer;
		transition: all 0.3s ease;
	}

	.btn-confirm.active {
		background: linear-gradient(135deg, #667eea, #764ba2);
		border-color: transparent;
		box-shadow: 0 4px 20px rgba(102, 126, 234, 0.3);
	}

	.btn-confirm-text {
		font-size: 15px;
		color: rgba(255, 255, 255, 0.4);
		letter-spacing: 2px;
		font-weight: 500;
	}

	.btn-confirm.active .btn-confirm-text {
		color: #fff;
	}

	.btn-skip {
		padding: 8px 20px;
		cursor: pointer;
	}

	.btn-skip-text {
		font-size: 14px;
		color: rgba(255, 255, 255, 0.3);
		letter-spacing: 1px;
	}

	.btn-skip:active .btn-skip-text {
		color: rgba(255, 255, 255, 0.6);
	}
</style>

<template>
  <Transition name="nested" appear>
		<div class="item item-right">
			<div :class="{'bubble bubble-right': true, 'large': isLarge}">
				<span class="inner">
					{{ msg }}
				</span>
			</div>
			<div class="avatar">
				<img
					:src="avatarURL"
					alt="avatar"
				/>
			</div>
		</div>
	</Transition>
</template>

<script setup>
defineProps({
  msg: String,
	avatarURL: String,
	isLarge: Boolean,
})
</script>

<style scoped>
/* ==== Animations ==== */
.nested-enter-active {
	transition: all 0.5s ease-in-out;
}

.nested-enter-from {
	transform: translateX(30px);
	opacity: 0;
}

.nested-enter-active .inner {
	transition: all 0.5s ease-in-out;
}

.nested-enter-active .inner {
	transition-delay: 0.2s;
}

.nested-enter-from .inner {
	opacity: 0.25;
}

/* ==== Static Styles ==== */
.bubble {
	max-width: 340px;
	padding: 10px;
	border-radius: 5px;
	position: relative;
	color: #000;
	word-wrap: break-word;
	word-break: normal;
	text-align: left;
}

.large {
	max-width: 380px;
}

.item-right .bubble {
	margin-right: 15px;
	background-color: #9eea6a;
}
.item-right .bubble:before {
	content: "";
	position: absolute;
	width: 0;
	height: 0;
	border-left: 10px solid #9eea6a;
	border-top: 10px solid transparent;
	border-right: 10px solid transparent;
	border-bottom: 10px solid transparent;
	right: -20px;
}
.item {
	margin-top: 15px;
	display: flex;
	width: 100%;
}
.item.item-right {
	justify-content: flex-end;
}
.avatar img {
	width: 35px;
	height: 35px;
	border-radius: 50%;
  object-fit: cover;
}
</style>
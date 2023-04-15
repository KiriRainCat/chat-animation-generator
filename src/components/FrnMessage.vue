<template>
  <Transition name="nested" appear>
		<div class="item item-left">
			<div class="avatar">
				<img
					:src="avatarURL"
					alt="avatar"
				/>
			</div>
			<div :class="{'bubble bubble-left': true, 'large': isLarge}">
				<span class="inner">
					{{ msg }}
				</span>
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
	transform: translateX(-30px);
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
.item-left .bubble {
	margin-left: 15px;
	background-color: #fff;
}
.item-left .bubble:before {
	content: "";
	position: absolute;
	width: 0;
	height: 0;
	border-left: 10px solid transparent;
	border-top: 10px solid transparent;
	border-right: 10px solid #fff;
	border-bottom: 10px solid transparent;
	left: -20px;
}
.item {
	margin-top: 15px;
	display: flex;
	width: 100%;
}
.item.item-center {
	justify-content: center;
}
.item.item-center span {
	font-size: 12px;
	padding: 2px 4px;
	color: #fff;
	background-color: #dadada;
	border-radius: 3px;
	user-select: none;
}
.avatar img {
	width: 35px;
	height: 35px;
	border-radius: 50%;
  object-fit: cover;
}
</style>
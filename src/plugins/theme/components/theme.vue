<template>
	<div class="cl-theme__icon" @click="open">
		<el-badge type="primary" is-dot>
			<cl-svg name="icon-discover" :size="16" />
		</el-badge>
	</div>

	<div v-if="theme.name == 'default'" class="cl-theme-dark">
		<el-switch v-model="isDark" inline-prompt :active-icon="Moon" :inactive-icon="Sunny" />
	</div>

	<el-drawer
		v-model="visible"
		title="设置主题"
		size="350px"
		modal-class="drawer-theme"
		append-to-body
	>
		<div class="cl-theme__drawer">
			<el-form label-position="top">
				<el-form-item label="推荐">
					<ul class="cl-theme__comd">
						<li v-for="(item, name) in themes" :key="name" @click="setComd(item)">
							<div
								class="w"
								:style="{
									backgroundColor: item.color
								}"
							>
								<check v-show="item.color == form.theme.color" />
							</div>

							<span>{{ item.label }}</span>
						</li>
					</ul>
				</el-form-item>

				<el-form-item label="自定义主色">
					<el-color-picker v-model="form.color" @change="setColor" />
					<span
						:style="{
							marginLeft: '10px'
						}"
						>{{ form.color }}</span
					>
				</el-form-item>

				<el-form-item label="菜单分组显示">
					<el-switch v-model="form.theme.isGroup" @change="setGroup"></el-switch>
				</el-form-item>

				<el-form-item label="转场动画">
					<el-switch
						v-model="form.theme.transition"
						active-value="slide"
						inactive-value="none"
						@change="setTransition"
					></el-switch>
				</el-form-item>
			</el-form>
		</div>
	</el-drawer>
</template>

<script lang="ts" setup name="cl-theme">
import { reactive, ref } from 'vue';
import { Check, Moon, Sunny } from '@element-plus/icons-vue';
import { ElMessage } from 'element-plus';
import { useBase } from '/$/base';
import { useDark } from '@vueuse/core';
import { storage } from '/@/cool';
import type { Theme } from '../types';
import { setTheme, themes } from '../utils';

const { menu } = useBase();

// 是否暗黑模式
const isDark = ref(useDark());

// 当前主题
const theme = reactive<Theme>(storage.get('theme'));

// 表单
const form = reactive<{ color: string; theme: Theme }>({
	color: theme.color || '',
	theme
});

// 抽屉
const visible = ref(false);

// 打开
function open() {
	visible.value = true;
}

// 清除暗黑模式
function clearDark() {
	isDark.value = false;
}

// 设置颜色
function setColor(color: any) {
	setTheme({ color });
	clearDark();
}

// 设置推荐
function setComd(item: any) {
	Object.assign(form.theme, item);
	form.color = item.color;
	setTheme(item);
	clearDark();
	ElMessage.success(`切换主题：${item.label}`);
}

// 设置分组
function setGroup(val: any) {
	setTheme({ isGroup: val });
	menu.setMenu();
}

// 设置转场动画
function setTransition(val: any) {
	setTheme({ transition: val });
}
</script>

<style lang="scss">
.cl-theme {
	&-dark {
		width: 45px;
		margin-left: 10px;
	}

	&__comd {
		display: flex;
		flex-wrap: wrap;

		li {
			display: inline-flex;
			align-items: center;
			list-style: none;
			margin-right: 15px;
			cursor: pointer;

			.w {
				height: 20px;
				width: 20px;
				border-radius: 4px;
				margin: 5px 10px 5px 0;
				text-align: center;
				color: #fff;
				line-height: 20px;
				padding: 2px;
				box-sizing: border-box;

				.el-icon {
					height: 100%;
					width: 100%;
				}
			}

			&:hover {
				opacity: 0.7;
			}
		}
	}

	&__icon {
		display: flex;
		align-items: center;
		justify-content: center;
		height: 100%;
		width: 45px;

		.el-badge {
			display: flex;
		}

		&:hover {
			color: var(--color-primary);
		}
	}
}

.drawer-theme {
	.el-drawer__header {
		padding: 20px 20px;
		margin-bottom: 0;
	}

	.el-drawer__title {
		font-size: 16px;
	}
}
</style>

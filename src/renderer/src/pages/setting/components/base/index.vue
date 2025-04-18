<template>
  <div class="setting-base-container">
    <t-form ref="form" label-width="110px" :data="formData" label-align="left">
      <t-form-item :label="$t('pages.setting.base.theme')" name="theme">
        <t-radio-group v-model="formData.theme">
          <div v-for="(item, index) in MODE_OPTIONS" :key="index" class="setting-layout-drawer">
            <div>
              <t-radio-button :key="index" :value="item.type">
                <component :is="getModeIcon(item.type)" class="mode-img" />
              </t-radio-button>
              <p :style="{ textAlign: 'center', marginTop: '8px' }">{{ item.text }}</p>
            </div>
          </div>
        </t-radio-group>
      </t-form-item>
      <t-form-item :label="$t('pages.setting.base.bossKey')" name="shortcutKey">
        <t-space align="center">
          <t-input ref="shortcutInputRef" v-model="formatShortcut" class="shortcut-content"
            :placeholder="placeholderShortcut" :status="statusShortcut" :tips="tipShortcut" :style="{ width: '255px' }"
            @keydown="getShortKeys" @focus="focusShortcut" @blur="blurShortcut">
            <template #suffix>
              <div @click.stop="cancelShortcut">
                <close-icon />
              </div>
            </template>
          </t-input>
          <span class="title" @click="reset('shortcut')">{{ $t('pages.setting.base.reset') }}</span>
        </t-space>
      </t-form-item>
      <t-form-item :label="$t('pages.setting.base.timeout')" name="timeout">
        <div class="timeout">
          <t-space align="center">
            <t-input-number v-model="formData.timeout" theme="column" class="timeout-content"
              :placeholder="t('pages.setting.placeholder.general')"
              :style="{ width: '255px' }"
              :min="5000"
              :max="1000 * 60"
              @change="changeTimeoutEvent"
            />
            <span class="title" @click="reset('timeout')">{{ $t('pages.setting.base.reset') }}</span>
          </t-space>
        </div>
      </t-form-item>
      <t-form-item :label="$t('pages.setting.base.film')" name="film">
        <div class="film">
          <t-space direction="vertical">
            <t-space align="center">
              <t-select v-model="formData.defaultHot" :placeholder="$t('pages.setting.placeholder.general')"
                :label="$t('pages.setting.base.hotRecommend')" :style="{ width: '255px' }">
                <t-option value="kylive" :label="$t('pages.setting.base.kylive')"></t-option>
                <t-option value="enlightent" :label="$t('pages.setting.base.enlightent')"></t-option>
                <t-option value="douban" :label="$t('pages.setting.base.douban')"></t-option>
                <t-option value="komect" :label="$t('pages.setting.base.komect')"></t-option>
              </t-select>
            </t-space>
            <t-space align="center">
              <t-select v-model="formData.defaultSearchType" :placeholder="$t('pages.setting.placeholder.general')"
                :label="$t('pages.setting.base.search')" :style="{ width: '120px' }">
                <t-option value="site" :label="$t('pages.setting.base.site')"></t-option>
                <t-option value="group" :label="$t('pages.setting.base.group')"></t-option>
                <t-option value="all" :label="$t('pages.setting.base.all')"></t-option>
              </t-select>
              <t-select v-model="formData.defaultFilterType" :placeholder="$t('pages.setting.placeholder.general')"
                :label="$t('pages.setting.base.filter')" :style="{ width: '120px' }">
                <t-option :value="true" :label="$t('pages.setting.base.on')"></t-option>
                <t-option :value="false" :label="$t('pages.setting.base.off')"></t-option>
              </t-select>
            </t-space>
          </t-space>
        </div>
      </t-form-item>
      <t-form-item :label="$t('pages.setting.base.iptv')" name="iptv">
        <div class="iptv">
          <t-space direction="vertical">
            <t-space align="center">
              <t-radio v-model="formData.iptvMarkIp" allow-uncheck>{{ $t('pages.setting.base.markIp') }}</t-radio>
              <t-radio v-model="formData.iptvDelay" allow-uncheck>{{ $t('pages.setting.base.delay') }}</t-radio>
              <t-radio v-model="formData.iptvThumbnail" allow-uncheck>{{ $t('pages.setting.base.thumbnail') }}</t-radio>
              <span class="title" @click="isVisible.iptvThumbnail = true">{{ $t('pages.setting.base.info') }}</span>
            </t-space>
            <t-space align="center">
              <t-input v-model="formData.defaultIptvEpg" :label="$t('pages.setting.base.defaultEpg')"
                :placeholder="$t('pages.setting.placeholder.epgTip')" :style="{ width: '255px' }" />
              <span class="title" @click="reset('epg')">{{ $t('pages.setting.base.reset') }}</span>
            </t-space>
            <t-space align="center">
              <t-input v-model="formData.defaultIptvLogo" :label="$t('pages.setting.base.globalLogo')"
                :placeholder="$t('pages.setting.placeholder.logoTip')" :style="{ width: '255px' }" />
              <span class="title" @click="reset('logo')">{{ $t('pages.setting.base.reset') }}</span>
            </t-space>
          </t-space>
        </div>
      </t-form-item>
      <t-form-item :label="$t('pages.setting.base.player')" name="player">
        <div class="player">
          <t-space direction="vertical">
            <t-space align="center">
              <t-select v-model="formData.playerMode.type" :options="PLAYER_OPTIONS"
                :placeholder="$t('pages.setting.placeholder.general')" :style="{ width: '255px' }" />
              <span class="title" @click="snifferEvent">{{ $t('pages.setting.base.sniffer') }}</span>
              <span class="title" @click="barrageEvent">{{ $t('pages.setting.base.barrage') }}</span>
            </t-space>
            <t-space align="center" v-if="formData.playerMode.type === 'custom'">
              <t-input v-model="formData.playerMode.external" :label="$t('pages.setting.base.command')"
                :placeholder="$t('pages.setting.placeholder.general')" :style="{ width: '255px' }" />
              <span class="title" @click="isVisible.customPlayer = true">{{ $t('pages.setting.base.info') }}</span>
            </t-space>
          </t-space>
        </div>
      </t-form-item>
      <t-form-item :label="$t('pages.setting.base.security')" name="security">
        <t-space>
          <span v-if="platform !== 'linux'" class="title" @click="openProxySetting">
            {{ $t('pages.setting.base.proxy') }}
          </span>
          <span class="title" @click="safeEvnet('ua')">{{ $t('pages.setting.base.ua') }}</span>
          <span class="title" @click="safeEvnet('dns')">{{ $t('pages.setting.base.dns') }}</span>
        </t-space>

        <dialog-ua-view v-model:visible="isVisible.ua" :data="safeDialogData" @submit="flushDialogData" />
        <dialog-dns-view v-model:visible="isVisible.dns" :data="safeDialogData" @submit="flushDialogData" />
      </t-form-item>
      <t-form-item :label="$t('pages.setting.base.permission')" name="permission">
        <t-space>
          <t-radio v-if="platform !== 'linux'" v-model="formData.selfBoot" allow-uncheck @change="selefBootEvnet">
            {{ $t('pages.setting.base.selefBoot') }}
          </t-radio>
          <!-- <t-radio v-model="formData.hardwareAcceleration" allow-uncheck @change="hardwareAccelerationEvnet">
            {{ $t('pages.setting.base.hardwareAcceleration') }}
          </t-radio> -->
          <t-radio v-model="formData.windowPosition.status" allow-uncheck @change="windowPositionEvnet">
            {{ $t('pages.setting.base.windowPosition') }}
          </t-radio>
          <t-radio v-model="formData.debug" allow-uncheck @change="debugEvnet">
            {{ $t('pages.setting.base.debug') }}
          </t-radio>
        </t-space>
      </t-form-item>
      <t-form-item :label="$t('pages.setting.base.other')" name="other">
        <t-space>
          <span class="title" @click="resetOriginal">{{ $t('pages.setting.base.restoreFactory') }}</span>
          <span class="title" @click="dataMange">{{ $t('pages.setting.base.dataMange') }}</span>
          <span class="title" @click="isVisible.update = true">{{ $t('pages.setting.base.checkUpdate') }}</span>
          <span class="title" @click="isVisible.disclaimer = true">{{ $t('pages.setting.base.disclaimer') }}</span>
        </t-space>

        <dialog-custom-player v-model:visible="isVisible.customPlayer" />
        <dialog-barrage-view v-model:visible="isVisible.barrage" :data="barrageDialogData" @submit="flushDialogData" />
        <dialog-data-view v-model:visible="isVisible.data" :webdev="webdevDialogData" @submit="flushDialogData" />
        <dialog-update-view v-model:visible="isVisible.update" />
        <dialog-thumbnail-view v-model:visible="isVisible.iptvThumbnail" />
        <dialog-sniffer-view v-model:visible="isVisible.sniffer" :data="snifferDialogData" @submit="flushDialogData" />
        <dialog-disclaimer-view v-model:visible="isVisible.disclaimer" />
      </t-form-item>
    </t-form>
  </div>
</template>

<script setup lang="ts">
import { CloseIcon } from 'tdesign-icons-vue-next';
import { DialogPlugin, MessagePlugin } from 'tdesign-vue-next';
import { computed, onMounted, onActivated, ref, watch, reactive, defineAsyncComponent, shallowRef } from 'vue';

import { fetchSettingList, clearDb, sourceSetting } from '@/api/setting';
import { usePlayStore, useSettingStore } from '@/store';
import { t } from '@/locales';
import emitter from '@/utils/emitter';

import SettingAutoIcon from '@/assets/assets-setting-auto.svg';
import SettingDarkIcon from '@/assets/assets-setting-dark.svg';
import SettingLightIcon from '@/assets/assets-setting-light.svg';
// import DialogDataView from './components/DialogData.vue';
// import DialogUaView from './components/DialogUA.vue';
// import DialogDnsView from './components/DialogDns.vue';
// import DialogUpdateView from './components/DialogUpdate.vue';
// import DialogThumbnailView from './components/DialogThumbnail.vue';
// import DialogCustomPlayer from './components/DialogCustomPlayer.vue';
// import DialogSnifferView from './components/DialogSniffer.vue';
// import DialogBarrageView from './components/DialogBarrage.vue';
// import DialogDisclaimerView from '@/pages/Disclaimer.vue';

const DialogDataView = shallowRef(defineAsyncComponent(() => import('./components/DialogData.vue')));
const DialogUaView = shallowRef(defineAsyncComponent(() => import('./components/DialogUA.vue')));
const DialogDnsView = shallowRef(defineAsyncComponent(() => import('./components/DialogDns.vue')));
const DialogUpdateView = shallowRef(defineAsyncComponent(() => import('./components/DialogUpdate.vue')));
const DialogThumbnailView = shallowRef(defineAsyncComponent(() => import('./components/DialogThumbnail.vue')));
const DialogCustomPlayer = shallowRef(defineAsyncComponent(() => import('./components/DialogCustomPlayer.vue')));
const DialogSnifferView = shallowRef(defineAsyncComponent(() => import('./components/DialogSniffer.vue')));
const DialogBarrageView = shallowRef(defineAsyncComponent(() => import('./components/DialogBarrage.vue')));
const DialogDisclaimerView = shallowRef(defineAsyncComponent(() => import('@/pages/Disclaimer.vue')));

const remote = window.require('@electron/remote');
const win = remote.getCurrentWindow();

const { platform } = window.electron.process;

const isVisible = reactive({
  data: false,
  update: false,
  dns: false,
  ua: false,
  iptvThumbnail: false,
  sniffer: false,
  customPlayer: false,
  barrage: false,
  disclaimer: false,
});

const safeDialogData = ref({ data: '', type: 'dns' });
const webdevDialogData = ref({ sync: false, data: { url: "https://dav.jianguoyun.com/dav/", username: "", password: "" } });
const snifferDialogData = ref({ data: { type: '', url: '' }, type: 'snifferMode' });
const barrageDialogData = ref({ data: { url: '', key: '', support: [], start: '', mode: '', color: '', content: '' }, type: 'barrage' });

const MODE_OPTIONS = computed(() => {
  return [
    { type: 'light', text: t('pages.setting.base.light') },
    { type: 'dark', text: t('pages.setting.base.dark') },
    { type: 'auto', text: t('pages.setting.base.auto') }
  ]
});

const PLAYER_OPTIONS = computed(() => {
  return [
    { label: t('pages.setting.base.xgplayer'), value: 'xgplayer' },
    // { label: t('pages.setting.base.dplayer'), value: 'dplayer' },
    { label: t('pages.setting.base.artplayer'), value: 'artplayer' },
    // { label: t('pages.setting.base.nplayer'), value: 'nplayer' },
    { label: t('pages.setting.base.oplayer'), value: 'oplayer' },
    { label: t('pages.setting.base.custom'), value: 'custom' }
  ]
});

const shortcutInputRef = ref<HTMLInputElement | null>(null);
const placeholderShortcut = ref(t('pages.setting.placeholder.shortcutKeyTip'));
const statusShortcut = ref('default');
const tipShortcut = ref('');

const getModeIcon = (mode: string) => {
  const modeIconMap = {
    light: SettingLightIcon,
    dark: SettingDarkIcon,
    auto: SettingAutoIcon,
  };
  return modeIconMap[mode];
};

const storePlayer = usePlayStore();
const storeSetting = useSettingStore();
const theme = computed(() => {
  return storeSetting.getStateMode;
});

const formData = ref({
  version: '3.3.8',
  theme: 'auto',
  lang: 'zh_CN',
  defaultHot: 'kylive',
  defaultSearchType: 'site',
  defaultFilterType: false,
  defaultIptvEpg: 'https://epg.112114.eu.org/?ch={name}&date={date}',
  defaultIptvLogo: 'https://epg.112114.eu.org/logo/{name}.png',
  timeout: 5000,
  iptvMarkIp: true,
  iptvThumbnail: false,
  iptvDelay: false,
  defaultSite: null,
  defaultIptv: null,
  defaultAnalyze: null,
  analyzeFlag: [
    'youku', 'qq',
    'iqiyi', 'qiyi',
    'letv', 'sohu',
    'tudou', 'pptv',
    'mgtv'
  ],
  defaultDrive: '',
  defaultViewCasual: '',
  playerMode: {
    type: 'artplayer',
    external: ''
  },
  snifferMode: {
    type: 'pie',
    url: ''
  },
  barrage: {
    url: '',
    key: '',
    support: [],
    start: '',
    mode: '',
    color: '',
    content: ''
  },
  softSolution: false,
  skipStartEnd: false,
  agreementMask: false,
  recordShortcut: '',
  selfBoot: false,
  hardwareAcceleration: true,
  ua: '',
  communitySubscribe: '',
  webdev: { sync: false, data: { url: "https://dav.jianguoyun.com/dav/", username: "", password: "" } },
  windowPosition: { status: false, position: { width: 1000, height: 640 } },
  debug: false
});

const tmp = reactive({
  recordedSourceShortcut: ''
});

watch(theme, (newValue) => {
  formData.value.theme = newValue;
});

watch(formData,
  (newValue, _) => {
    storeSetting.updateConfig({
      mode: formData.value.theme,
      timeout: formData.value.timeout < 5000 ? 5000 : formData.value.timeout
    });
    storePlayer.updateConfig({
      setting: {
        playerMode: formData.value.playerMode,
        snifferMode: formData.value.snifferMode,
        barrage: formData.value.barrage
      },
    } as any);
    if (newValue) {
      sourceSetting(newValue)
    }
  },
  {
    deep: true
  }
);

// 监听刷新hot
watch(
  () => [
    formData.value.defaultHot,
    formData.value.defaultSearchType,
    formData.value.defaultFilterType,
  ],
  (_, oldValue) => {
    if (oldValue.every((item) => typeof item !== 'undefined')) {
      emitter.emit('refreshHotConfig');
    }
  },
);

// 监听刷新iptv
watch(
  () => [formData.value.iptvMarkIp, formData.value.iptvDelay, formData.value.iptvThumbnail, formData.value.defaultIptvEpg],
  (_, oldValue) => {
    if (oldValue.every((item) => typeof item !== 'undefined')) {
      emitter.emit('refreshIptvConfig');
    }
  },
);

onMounted(() => {
  getData();
});

onActivated(() => {
  const isListenedRefreshSetting = emitter.all.get('refreshSetting');
  if (!isListenedRefreshSetting) emitter.on('refreshSetting', refreshSetting);
});

const getData = async () => {
  formData.value = await fetchSettingList();
};

const openProxySetting = () => {
  window.electron.ipcRenderer.send('open-proxy-setting');
};

// 出厂恢复
const resetOriginal = () => {
  const handleClear = async () => {
    await clearDB();
    await clearCache();

    confirmDia.hide();
    MessagePlugin.success(t('pages.setting.message.reboot'));
    setTimeout(() => {
      window.electron.ipcRenderer.send('reboot-app');
    }, 1000);
  };

  const confirmDia = DialogPlugin({
    body: t('pages.setting.dialog.restoreFactoryBody'),
    header: t('pages.setting.dialog.restoreFactoryHeader'),
    width: '320px',
    confirmBtn: t('pages.setting.dialog.confirm'),
    cancelBtn: t('pages.setting.dialog.cancel'),
    placement: 'center',
    closeBtn: '',
    onConfirm: handleClear,
    onClose: () => confirmDia.hide(),
  });
};

// 清除数据库
const clearDB = async () => {
  await clearDb(["reset", "cache"]);
};

// 清除缓存
const clearCache = async () => {
  const { session } = win.webContents;
  await session.clearCache();
};

// 组合键格式化
const formatShortcut = computed(() => {
  const val = formData.value.recordShortcut;
  if (!val) return '';
  let shortcut;
  shortcut = val
    .replaceAll('+', ' + ')
    .replace('Up', '↑')
    .replace('Down', '↓')
    .replace('Right', '→')
    .replace('Left', '←')
    .replace('Space', '空格');
  if (platform === 'darwin') {
    shortcut = val
      .replace('CommandOrControl', '⌘')
      .replace('Command', '⌘')
      .replace('Meta', '⌘')
      .replace('Alt', '⌥')
      .replace('Control', '⌃')
      .replace('Shift', '⇧');
  }
  shortcut.replace('CommandOrControl', 'Ctrl');
  return shortcut;
});

// 设置组合键更换焦点placeholder
const focusShortcut = () => {
  // 复制快捷键
  tmp.recordedSourceShortcut = formData.value.recordShortcut;
  formData.value.recordShortcut = '';
  placeholderShortcut.value = t('pages.setting.placeholder.shortcutKeyEnterTip');
};

// 设置组合键更换失去焦点placeholder
const blurShortcut = () => {
  // 还原快捷键
  if (tmp.recordedSourceShortcut && !formData.value.recordShortcut)
    formData.value.recordShortcut = tmp.recordedSourceShortcut;
  placeholderShortcut.value = t('pages.setting.placeholder.shortcutKeyTip');
};

// 获取组合按键
const getShortKeys = (_, event) => {
  const { e } = event;

  e.preventDefault();
  const str = formData.value.recordShortcut;
  // 已存储按键则跳过
  if (str.includes(e.key)) return;

  // 获取有没有按下特殊按键【'Control', 'Alt', 'Shift', 'Meta'】
  const auxiliaryKey = [
    e.ctrlKey ? 'Ctrl' : '',
    e.altKey ? 'Alt' : '',
    e.shiftKey ? 'Shift' : '',
    e.metaKey ? 'Meta' : '',
  ].filter((t) => t);

  // 获取普通字符【'0-9'，'a-Z'，'f1-f12'，'符号'】
  let publicKey = '';
  if (e.keyCode >= 65 && e.keyCode <= 90) {
    // A-Z
    publicKey = e.code.replace('Key', '');
  } else if (e.keyCode >= 48 && e.keyCode <= 57) {
    // 0-9
    publicKey = e.code.replace('Digit', '');
  } else if (e.keyCode >= 112 && e.keyCode <= 123) {
    // F1-F12
    publicKey = e.code;
  } else if (['ArrowRight', 'ArrowLeft', 'ArrowUp', 'ArrowDown'].includes(e.key)) {
    // Arrows
    publicKey = e.code.replace('Arrow', '');
  } else if (['=', '-', '~', '[', ']', ';', "'", ',', '.', '/'].includes(e.key)) {
    publicKey = e.key;
  }

  if (auxiliaryKey.length) {
    formData.value.recordShortcut = `${auxiliaryKey.join('+')}+${publicKey}`;
  } else {
    formData.value.recordShortcut = str.substring(0, str.lastIndexOf('+') + 1) + publicKey;
  }
  isLegalShortcut(formData.value.recordShortcut);
};

// 判断快捷键是否合法
const isLegalShortcut = (item) => {
  const specialKeys = ['Crl', 'Alt', 'Shift', 'Meta'];
  const publicKeys = [
    '=',
    '-',
    '~',
    '[',
    ']',
    ';',
    "'",
    ',',
    '.',
    '/',
    'A',
    'B',
    'C',
    'D',
    'E',
    'F',
    'G',
    'H',
    'I',
    'J',
    'K',
    'L',
    'M',
    'N',
    'O',
    'P',
    'Q',
    'R',
    'S',
    'T',
    'U',
    'V',
    'W',
    'X',
    'Y',
    'Z',
    0,
    1,
    2,
    3,
    4,
    5,
    6,
    7,
    8,
    9,
    'Right',
    'Left',
    'Up',
    'Down',
  ];
  let isPubilcKeys = false;
  let isSpecialKeys = false;

  const keys = item.split('+');

  keys.forEach((key) => {
    if (specialKeys.includes(key)) isSpecialKeys = true;
    if (publicKeys.includes(key)) isPubilcKeys = true;
  });
  if (isPubilcKeys && isSpecialKeys) {
    statusShortcut.value = 'default';
    tipShortcut.value = '';
    shortcutInputRef.value!.blur();
    window.electron.ipcRenderer.send('update-shortcut', formData.value.recordShortcut);
  } else {
    tipShortcut.value = t('pages.setting.placeholder.shortcutKeyNonCompliance');
    statusShortcut.value = 'error';
  }
};

// 取消快捷键
const cancelShortcut = () => {
  console.log('cancelShortcut');
  formData.value.recordShortcut = '';
  placeholderShortcut.value = t('pages.setting.placeholder.shortcutKeyTip');
  tipShortcut.value = '';
  window.electron.ipcRenderer.send('uninstall-shortcut');
};

// 重置
const reset = (type: string) => {
  console.log(`reset:${type}`);
  if (type === 'shortcut') {
    if (platform === 'darwin') formData.value.recordShortcut = 'Shift+Command+Z';
    else formData.value.recordShortcut = 'Shift+Alt+Z';
    shortcutInputRef.value!.blur();
    tipShortcut.value = '';
    placeholderShortcut.value = t('pages.setting.placeholder.shortcutKeyTip');
    window.electron.ipcRenderer.send('update-shortcut', formData.value.recordShortcut);
  } else if (type === 'epg') {
    formData.value.defaultIptvEpg = 'https://epg.112114.eu.org/?ch={name}&date={date}';
  } else if (type === 'logo') {
    formData.value.defaultIptvLogo = 'https://epg.112114.eu.org/logo/{name}.png';
  } else if (type === 'danmuku') {
    formData.value.barrage.url = 'https://dm.bbj.icu/dm?ac=dm';
  } else if (type === 'viewCasual') {
    formData.value.defaultViewCasual = 'http://api.yujn.cn/api/zzxjj.php';
  } else if (type === 'timeout') {
    formData.value.timeout = 5000;
    window.electron.ipcRenderer.send('update-global', 'timeout', formData.value.timeout);
  }
};

// 开机自启
const selefBootEvnet = () => {
  console.log('开机自启', formData.value.selfBoot);
  window.electron.ipcRenderer.send('toggle-selfBoot', formData.value.selfBoot);
};

// 硬件加速
const hardwareAccelerationEvnet = () => {
  console.log('开机自启', formData.value.hardwareAcceleration);
  window.electron.ipcRenderer.send('update-global', 'hardwareAcceleration', formData.value.hardwareAcceleration);
  MessagePlugin.success(
    formData.value.hardwareAcceleration ? t('pages.setting.message.hardwareAccelerationOn') : t('pages.setting.message.hardwareAccelerationOff'),
  );
};

// 退出保存主窗口大小及位置
const windowPositionEvnet = () => {
  console.log('窗口位置', formData.value.windowPosition);
  MessagePlugin.success(
    formData.value.windowPosition.status ? t('pages.setting.message.windowPositionOn') : t('pages.setting.message.windowPositionOff'),
  );
};

const debugEvnet = () => {
  console.log('调试', formData.value.debug);
  window.electron.ipcRenderer.send('update-global', 'debug', formData.value.debug);
  window?.location.reload();
};


const safeEvnet = (type: string) => {
  safeDialogData.value = {
    data: formData.value[type],
    type,
  };
  isVisible[type] = true;
};

const snifferEvent = () => {
  const { snifferMode } = formData.value;
  snifferDialogData.value = {
    data: { ...snifferMode },
    type: 'snifferMode',
  };

  isVisible.sniffer = true;
}

const barrageEvent = () => {
  const { barrage } = formData.value;
  barrageDialogData.value = {
    data: { ...barrage },
    type: 'barrage',
  };

  isVisible.barrage = true;
}

const dataMange = () => {
  let { webdev } = formData.value;
  webdevDialogData.value = webdev ? webdev : { sync: false, data: { url: "https://dav.jianguoyun.com/dav/", username: "", password: "" } };
  isVisible.data = true
};

const flushDialogData = (item) => {
  const { data, type } = item;
  formData.value[type] = data;
};

const changeTimeoutEvent = (item: number) => {
  if (item < 5000 || item > 1000 * 60) {
    formData.value.timeout = 5000;
  };
  window.electron.ipcRenderer.send('update-global', 'timeout', formData.value.timeout);
};

const refreshSetting = () => {
  console.log('[setting][bus][refresh]');
  getData();
};
</script>

<style lang="less" scoped>
.setting-base-container {
  height: 100%;
  padding: 0 var(--td-comp-paddingLR-xxl);
  overflow: auto;

  :deep(.t-radio-group.t-size-m .t-radio-button) {
    height: auto;
  }

  :deep(.t-form__label) {
    label {
      font-weight: 500;
    }
  }

  .title {
    color: var(--td-brand-color);
    cursor: pointer;
    font-weight: 500;
  }

  .setting-layout-drawer {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-right: 35px;
    box-sizing: content-box;

    .t-radio-button {
      display: inline-flex;
      max-height: 78px;
      padding: 0;
      border-radius: var(--td-radius-default);
      border: 2px solid transparent;

      > :deep(.t-radio-button__label) {
        display: inline-flex;
        position: relative;

        .mode-img,
        .layout-img {
          border-radius: 9px;
        }
      }
    }

    .t-is-checked {
      border-radius: 10px;
      border: 2px solid var(--td-brand-color);
    }

    .t-form__controls-content {
      justify-content: end;
    }
  }
}
</style>

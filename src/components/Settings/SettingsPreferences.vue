<template>
  <div class="preferences">
    <!-- Basics -->
    <AppBox title="General">
      <SettingsField v-model="value$" field-id="preferences.mode" :options-field="optionsField" />

      <SettingsField v-if="value$.mode > 0"
                     v-model="value$"
                     field-id="preferences.advanced.scaleMode"
                     :options-field="optionsField" />

      <SettingsField v-if="value$.mode >= 1 && value$.advanced.scaleMode === 'padding'" v-model="value$" field-id="preferences.advanced.useColorPaddingStrip" />

      <SettingsField v-if="value$.mode >= 1 && !animated" v-model="value$" field-id="preferences.advanced.useClothTransparencyEffect" />
    </AppBox>

    <!-- Runs -->
    <section v-if="value$.mode === 2" id="preferences-runs" class="box">
      <div class="box__header">
        <h2 class="title">
          <span class="icon"><font-awesome-icon icon="running" /></span>
          <span>Per run.</span>
        </h2>
        <h3 class="subtitle">
          Customize body preferences for multiple runs.
        </h3>
      </div>

      <div class="box__content">
        <SettingsField
          v-model="value$"
          field-id="preferences.body.runs.mode" />

        <SettingsField v-if="value$.body.runs.mode !== false" v-model="value$" field-id="preferences.body.runs.count" />

        <SettingsField v-if="value$.body.runs.mode === 'increase'"
                       v-model="value$"
                       field-id="preferences.body.runs.rate"
                       :description="`Body preferences will increase ${value$.body.runs.rate} at each run.`" />
      </div>
    </section>

    <!-- Boobs -->
    <Preference id="preferences-body"
                v-model="value$.body.boobs"
                :preferences-mode="value$.mode"
                label="Boobs" />

    <!-- Areola -->
    <Preference v-model="value$.body.areola"
                :preferences-mode="value$.mode"
                label="Areola" />

    <!-- Nipple -->
    <Preference v-model="value$.body.nipple"
                :preferences-mode="value$.mode"
                label="Nipple" />

    <!-- Vagina -->
    <Preference v-model="value$.body.vagina"
                :preferences-mode="value$.mode"
                label="Vagina"
                :max="1.5" />

    <!-- Pubic Hair -->
    <Preference v-model="value$.body.pubicHair"
                :preferences-mode="value$.mode"
                label="Pubic Hair" />

    <!-- Advanced -->
    <section v-if="value$.mode > 1" id="preferences-advanced" class="box">
      <div class="box__header">
        <h2 class="title">
          <span class="icon"><font-awesome-icon icon="toolbox" /></span>
          <span>Advanced.</span>
        </h2>
        <h3 class="subtitle">
          Additional processing settings.
        </h3>
      </div>

      <div class="box__content">
        <SettingsField v-if="!isMacOS" v-model="value$" field-id="preferences.advanced.device" />

        <SettingsField v-else field-id="preferences.advanced.device" description="Mac only supports CPU.">
          <select v-model="value$.advanced.device" class="input" disabled>
            <option value="CPU">
              CPU
            </option>
          </select>
        </SettingsField>

        <SettingsField v-if="value$.advanced.scaleMode !== 'none'" v-model="value$" field-id="preferences.advanced.imageSize" />

        <SettingsField v-model="value$" field-id="preferences.advanced.compress" />
      </div>
    </section>

    <!-- Experimental -->
    <section v-if="value$.mode > 1" class="box">
      <div class="box__header">
        <h2 class="title">
          <span class="icon"><font-awesome-icon icon="flask" /></span>
          <span>Experimental.</span>
        </h2>
        <h3 class="subtitle">
          Options that could (or not) improve the fake nude.
        </h3>
      </div>

      <div class="box__content">
        <SettingsField v-model="value$" field-id="preferences.advanced.useArtifactsInpaint" />

        <SettingsField v-model="value$" field-id="preferences.advanced.useColorTransfer" />
      </div>
    </section>

    <!-- Waifu2X -->
    <section v-if="value$.mode > 1 && !animated" class="box">
      <div class="box__header">
        <h2 class="title">
          <span class="icon"><font-awesome-icon icon="expand" /></span>
          <span>Waifu2X.</span>
        </h2>
        <h3 class="subtitle">
          Settings for the upscale and denoise algorithm.
        </h3>
      </div>

      <div v-if="requirements.canUseWaifu" class="box__content">
        <SettingsField v-model="value$" field-id="preferences.advanced.waifu.enabled" />

        <div v-if="value$.advanced.waifu.enabled">
          <SettingsField v-model="value$" field-id="preferences.advanced.waifu.scale" />
          <SettingsField v-model="value$" field-id="preferences.advanced.waifu.denoise" />
          <SettingsField v-model="value$" field-id="preferences.advanced.waifu.tta" />
          <SettingsField v-model="value$" field-id="preferences.advanced.waifu.arch" />
        </div>
      </div>

      <div v-else class="box__content">
        <nuxt-link to="/wizard/waifu" class="button">
          Install Waifu2X.
        </nuxt-link>
      </div>
    </section>
  </div>
</template>

<script>
import { tutorial } from '~/modules'
import { requirements } from '~/modules/system'
import { VModel } from '~/mixins'

export default {
  mixins: [VModel],

  props: {
    animated: {
      type: Boolean,
      default: false,
    },
  },

  data: () => ({
    requirements,
  }),

  computed: {
    optionsField() {
      return this.animated ? 'animated-options' : 'options'
    },

    isMacOS() {
      return process.platform === 'darwin'
    },
  },

  mounted() {
    tutorial.preferences()
  },
}
</script>

<style lang="scss" scoped>
.preferences {
  &::v-deep {
    .box__header {
      .title {
        .icon {
          @apply mr-1;
        }
      }
    }
  }
}
</style>

<template>
  <div>
    <n-form-item label="An ipv4 address:" v-bind="validationAttrs">
      <n-input v-model:value="rawIpAddress" placeholder="An ipv4 address..." />
    </n-form-item>

    <n-divider style="margin-top: 0" mt-0 />

    <n-form-item
      v-for="{ label, value } of convertedSections"
      :key="label"
      :label="label"
      label-placement="left"
      label-width="100"
    >
      <input-copyable
        :value="validationAttrs.validationStatus === 'error' ? '' : value"
        placeholder="Set a correct ipv4 address"
      />
    </n-form-item>
  </div>
</template>

<script setup lang="ts">
import { useValidation } from '@/composable/validation';
import { convertBase } from '../integer-base-converter/integer-base-converter.model';
import { ipv4ToInt, ipv4ToIpv6, isValidIpv4 } from './ipv4-address-converter.service';

const rawIpAddress = useStorage('ipv4-converter:ip', '192.168.1.1');

const convertedSections = computed(() => {
  const ipInDecimal = ipv4ToInt({ ip: rawIpAddress.value });

  return [
    {
      label: 'Decimal : ',
      value: String(ipInDecimal),
    },
    {
      label: 'Hexadecimal: ',
      value: convertBase({ fromBase: 10, toBase: 16, value: String(ipInDecimal) }).toUpperCase(),
    },
    {
      label: 'Binary: ',
      value: convertBase({ fromBase: 10, toBase: 2, value: String(ipInDecimal) }),
    },
    {
      label: 'Ipv6: ',
      value: ipv4ToIpv6({ ip: rawIpAddress.value }),
    },
    {
      label: 'Ipv6 (short): ',
      value: ipv4ToIpv6({ ip: rawIpAddress.value, prefix: '::ffff:' }),
    },
  ];
});

const { attrs: validationAttrs } = useValidation({
  source: rawIpAddress,
  rules: [{ message: 'Invalid ipv4 address', validator: (ip) => isValidIpv4({ ip }) }],
});
</script>

<style lang="less" scoped></style>

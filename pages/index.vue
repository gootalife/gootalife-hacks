<template>
  <div class="bg">
    <logo></logo>
    <v-shell
      class="v-shell"
      :banner="banner"
      :shell_input="input"
      :commands="commands"
      @shell_output="prompt"
    ></v-shell>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator';
import vShell from '@/components/v-shell.vue';
import logo from '@/components/logo.vue';
import { bio } from '@/assets/bio/bio';

@Component({
  components: {
    vShell,
    logo
  }
})
export default class Index extends Vue {
  input = '';
  banner = {
    header: 'Wellcome to gootalife-hacks!! 😎<br>' + 'Enter "help" for more information. 💪',
    emoji: {},
    sign: `moso@gootalife-hacks:~$`
  };
  commands = [
    {
      name: 'whoami',
      desc: 'Show my bio',
      exec(args: string[]) {
        let output = JSON.stringify(bio, null, 2).replaceAll('\n', '<br>');
        return output;
      }
    },
    {
      name: 'web',
      desc: 'Show my web information',
      exec(args: string[]) {
        const info: { [key: string]: string } = {
          gitHub: 'https://github.com/gootalife',
          twitter: '???'
        };
        let output =
          'Usage:<br>' +
          '    show {-a|-k Key|-l}<br>' +
          'Options:<br>' +
          '    -a: Show the information of all keys.<br>' +
          '    -k: Show the information of the key.<br>' +
          '    -l: List the keys.<br>';
        switch (args[0]) {
          case '-a':
            const pad = '          ';
            output = Object.keys(info)
              .map(
                (key) =>
                  `${(key + pad).slice(0, pad.length)}: <a href="${info[key]}">${info[key]}</a><br>`
              )
              .join('');
            break;
          case '-k':
            if (info[args[1]]) {
              output = `${args[1]}: ${info[args[2]]}`;
            }
            break;
          case '-l':
            output = Object.keys(info)
              .map((key) => `${key}<br>`)
              .join('');
            break;
        }
        return output;
      }
    }
  ];
  a = '';

  head() {
    return {
      title: 'Home'
    };
  }

  prompt(value: string): void {
    this.a = value;
  }

  mounted() {}
}
</script>

<style scoped>
@font-face {
  font-family: 'shell';
  src: url('~/assets/fonts/PressStart2P-Regular.ttf') format('truetype');
}
.v-shell {
  font-size: 10pt;
  font-family: shell;
}
.bg {
  background-color: black !important;
}
</style>

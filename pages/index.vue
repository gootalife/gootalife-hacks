<template>
  <div class="container">
    <v-shell
      :banner="banner"
      :shell_input="input"
      :commands="commands"
      @shell_output="prompt"
    ></v-shell>
    <p>{{ a }}</p>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'nuxt-property-decorator';
import vShell from '@/components/v-shell.vue';

@Component({
  components: {
    vShell
  }
})
export default class Index extends Vue {
  private head() {
    return {
      title: 'Home'
    };
  }
  private input = '';
  private banner = {
    header: 'Wellcome to gootalife-hacks!!',
    subHeader: 'This is my portfilio😎',
    helpHeader: 'Enter "help" for more information. 💪',
    emoji: {},
    sign: `moso@gootalife-hacks:~$`
  };
  private commands = [
    {
      name: 'show',
      desc: 'Show my informations',
      exec(args: string[]) {
        const info: { [key: string]: string } = {
          github: 'https://github.com/gootalife',
          twitter: 'none'
        };
        let output =
          'Usage:<br>' +
          '    show {-k Key|-a|-l}<br>' +
          'Options:<br>' +
          '    -k 出力するキー<br>' +
          '    -a すべてのキーに関する情報を出力<br>' +
          '    -l キーを列挙する<br>';
        switch (args[0]) {
          case '-a':
            output = 'https://github.com/gootalife';
            break;
          case '-k':
            output = `${args[1]}: ${info[args[2]]}`;
          case '-l':
            output = Object.keys(info)
              .map((x) => `${x}<br>`)
              .join('');
            break;
        }
        return output;
      }
    },
    {
      name: 'uname',
      desc: 'Show the current terminal name',
      exec(args: string[]) {
        return navigator.appVersion;
      }
    }
  ];
  private a = '';
  private prompt(value: string): void {
    this.a = value;
  }
}
</script>

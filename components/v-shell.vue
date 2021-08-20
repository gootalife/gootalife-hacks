<template>
  <div @click="$refs.cmd.focus()">
    <div ref="terminal" id="container">
      <div v-if="banner" id="banner">
        <div v-if="banner.header" ref="header"></div>
      </div>
      <output ref="output"></output>
      <div id="input-line" class="input-line">
        <div class="prompt">
          <div>{{ banner.sign ? banner.sign : '>>' }}</div>
        </div>

        <input
          v-model="value"
          ref="cmd"
          @keydown.enter="cmd_enter($event)"
          @keydown.up="history_up()"
          @keydown.down="history_down()"
          @keydown.tab="cmd_tab($event)"
          class="cmdline"
          maxlength="50"
          spellcheck="false"
          autofocus
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    shell_input: {
      required: false
    },
    banner: {
      type: Object,
      required: false,
      default: () => {
        return {
          header: 'Vue Shell',
          sign: 'VueShell $'
        };
      }
    },
    commands: {
      type: Array
    }
  },
  data() {
    return {
      value: '',
      history_: [],
      histpos_: 0,
      histtemp_: 0
    };
  },
  computed: {
    allcommands() {
      var tab = [
        { name: 'help', desc: 'Show all of available commands.' },
        { name: 'clear', desc: 'Clear histories of this teaminal.' }
      ];

      if (this.commands) {
        this.commands.forEach(({ name, desc }) => {
          tab.push({ name, desc });
        });
      }

      return tab;
    }
  },
  watch: {
    shell_input(val) {
      this.output(val);
      this.$parent.send_to_terminal = '';
    }
  },
  methods: {
    history_up() {
      if (this.history_.length) {
        if (this.history_[this.histpos_]) {
          this.history_[this.histpos_] = this.value;
        } else {
          this.histtemp_ = this.value;
        }
      }
      // up 38
      this.histpos_--;
      if (this.histpos_ < 0) {
        this.histpos_ = 0;
      }
      this.value = this.history_[this.histpos_] ? this.history_[this.histpos_] : this.histtemp_;
    },
    history_down() {
      if (this.history_.length) {
        if (this.history_[this.histpos_]) {
          this.history_[this.histpos_] = this.value;
        } else {
          this.histtemp_ = this.value;
        }
      }
      this.histpos_++;
      if (this.histpos_ > this.history_.length) {
        this.histpos_ = this.history_.length;
      }
      this.value = this.history_[this.histpos_] ? this.history_[this.histpos_] : this.histtemp_;
    },
    cmd_tab(e) {
      e.preventDefault();
    },
    cmd_enter() {
      if (this.value) {
        this.history_[this.history_.length] = this.value;
        this.histpos_ = this.history_.length;
      }

      //   Duplicate current input and append to output section.
      var line = this.$refs.cmd.parentNode.cloneNode(true);
      line.removeAttribute('id');
      line.classList.add('line');
      var input = line.querySelector('input.cmdline');
      input.autofocus = false;
      input.readOnly = true;
      this.$refs.output.appendChild(line);

      if (this.value && this.value.trim()) {
        var args = this.value.split(' ').filter(function (val) {
          return val;
        });
        var cmd = args[0];
        args = args.splice(1); // Remove cmd from arg list.
      }

      if (cmd == 'clear') {
        this.$refs.output.innerHTML = '';
        this.value = '';
      } else if (cmd == 'help') {
        var commandsList = this.allcommands.map(({ name, desc }) => {
          if (desc) {
            const pad = '          ';
            return `${(name + pad).slice(0, pad.length)}: ${desc}`.replaceAll(' ', '&nbsp;');
          }
          return name;
        });

        this.output('<div class="ls-files">' + commandsList.join('<br>') + '</div>');
      } else {
        if (this.commands) {
          this.commands.forEach((command) => {
            if (cmd == command.name) {
              this.output(command.exec(args).replaceAll(' ', '&nbsp;'));
              this.value = cmd;
              return;
            }
          });
        }
        if (this.value.trim() != '') {
          this.$emit('shell_output', this.value);
        }
        this.value = '';
      }

      // Clear/setup line for next input.
    },
    output(str) {
      this.$refs.output.insertAdjacentHTML('beforeEnd', str);
      this.value = '';
      document.getElementById('input-line').scrollIntoView(true);
    }
  },
  mounted() {
    if (this.banner.header) {
      this.$refs.header.insertAdjacentHTML('afterbegin', this.banner.header);
    }
  }
};
</script>

<style lang="css" scoped>
#container {
  color: white;
  background-color: black;
  padding: 0.5em 1.5em 1em 1em;
}
#container output {
  clear: both;
  width: 100%;
}
#banner {
  margin-bottom: 15px;
}
.input-line {
  display: -webkit-flex;
  -webkit-box-orient: horizontal;
  -webkit-box-align: stretch;
  display: -moz-flex;
  -moz-box-orient: horizontal;
  -moz-box-align: stretch;
  display: flex;
  box-orient: horizontal;
  box-align: stretch;
  clear: both;
}
.input-line > div:nth-child(2) {
  -webkit-box-flex: 1;
  -moz-box-flex: 1;
  box-flex: 1;
}
.prompt {
  white-space: nowrap;
  color: #6fff30;
  margin-right: 7px;
  display: -webkit-flex;
  display: -moz-flex;
  display: flex;
  box-pack: center;
  box-orient: vertical;
  user-select: none;
}
.cmdline {
  outline: none;
  background-color: transparent;
  margin: 0;
  flex: 1 1 auto;
  font: inherit;
  border: none;
  color: inherit;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
.ls-files {
  height: 45px;
  column-width: 100px;
}
</style>

<template lang="html">
  <div
    @click="playOrPause"
    :style="style"
    class="player"
    :class="{on: isPlay}">
    <audio id="vmusic" :src="src" autoplay loop></audio>
  </div>
</template>

<script>
  export default {
    props: {
      src: {
        type: String,
        required: true
      },
      top: {
        type: String,
        default: '0px'
      },
      right: {
        type: String,
        default: '0px'
      }
    },
    data: function() {
      return {
        isPlay: true,
        style: {
          top: '0px',
          right: '0px'
        }
      };
    },
    ready: function() {
      var self = this;
      this.style.top = this.top;
      this.style.right = this.right;
      function log(info) {
        console.log(info);
        // alert(info);
      }

      function forceSafariPlayAudio() {
        audioEl.load(); // iOS 9   还需要额外的 load 一下, 否则直接 play 无效
        audioEl.play(); // iOS 7/8 仅需要 play 一下
      }
      var audioEl = document.getElementById('vmusic');
      // 可以自动播放时正确的事件顺序是
      // loadstart
      // loadedmetadata
      // loadeddata
      // canplay
      // play
      // playing
      //
      // 不能自动播放时触发的事件是
      // iPhone5  iOS 7.0.6 loadstart
      // iPhone6s iOS 9.1   loadstart -> loadedmetadata -> loadeddata -> canplay
      audioEl.addEventListener('loadstart', function() {
        log('loadstart');
      }, false);
      audioEl.addEventListener('loadeddata', function() {
        log('loadeddata');
      }, false);
      audioEl.addEventListener('loadedmetadata', function() {
        log('loadedmetadata');
      }, false);
      audioEl.addEventListener('canplay', function() {
        log('canplay');
      }, false);
      audioEl.addEventListener('play', function() {
        log('play');
        // 当 audio 能够播放后, 移除这个事件
        window.removeEventListener('touchstart', forceSafariPlayAudio, false);
      }, false);
      audioEl.addEventListener('playing', function() {
        log('playing');
      }, false);
      audioEl.addEventListener('pause', function() {
        log('pause');
      }, false);
      // 由于 iOS Safari 限制不允许 audio autoplay, 必须用户主动交互(例如 click)后才能播放 audio,
      // 因此我们通过一个用户交互事件来主动 play 一下 audio.
      window.addEventListener('touchstart', forceSafariPlayAudio, false);
      audioEl.src = this.src;

      document.addEventListener("WeixinJSBridgeReady", forceSafariPlayAudio, false);

      //this.$nextTick(function() {
      //  document.getElementById('vmusic').pause();
      //  document.getElementById('vmusic').play();
      //});
    },
    methods: {
      play: function () {
        $('#vmusic')[0].play();
        this.isPlay = true;
      },
      pause: function () {
        $('#vmusic')[0].pause();
        this.isPlay = false;
      },
      playOrPause: function() {
        if(this.isPlay) {
          $('#vmusic')[0].pause();
          this.isPlay = false;
        } else {
          $('#vmusic')[0].play();
          this.isPlay = true;
        }
      }
    }
  }
</script>

<style lang="css">
  @keyframes player {
    0% {
      transform: rotate(0);
    }
    100% {
      transform: rotate(360deg);
    }
  }

  .player {
    position: absolute;
    width: 56px;
    height: 56px;
    margin: 0;
    background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAABkCAYAAABw4pVUAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAA44SURBVHhe7VwHUFVXGjZiTdysqzt211UTiQWCIBIwQVZKkKI0CzZYEEUdR8W1rA6gUgKISYxx1MWCZlUUR4262EsQFOy99967zug4ut/HnMs8EeSV+4Sr58388+6775Zzvu/85z//f/5zKlSQH4mAREAiIBGQCEgEJAISAYmAREAiIBGQCEgEJAISAYmAREAiIBGQCEgEJAISAYmAREAiIBGQCEgENI3A69evq7169eoLiDMkCNIfEgUZh/8mUHgszkWIa3jtF7xX05UvL4UHmJ9DnCDhkJjLly8PzMvLC1q0aJH7+PHjnTw9Pds2bty4NcprSeExz/E/XsNreQ/vFc9wAjl/Li/100w5AF4TSC8CuXfv3p7R0dEdqlSp8pUCvKHfvJfP4LMEOb1AzN81A0hZFZQgAbB/3rx5czBbuCkklEQan7lgwQJ3voPvksQUwzaA+RMkACANIliGaoCx1wtiBvHdLENZNcRy9V4A0RqtdOz27dsDjAXW1Pv4bpaBZSlX4LzPwgAACwDgdfbs2bDk5OTvTAXV1PtZBpaFZULZKr1PLMr8Xah0VUi/ffv2BZvDThhLDsvCMtG2QKqXOVDvowCo6GdogQN27drVw1jgzH1ffn5+N5QzkmV9H5iU2TuEczcwNzc3yNygmvr8EydO0NinfbBOpbAZIWh93U0FS/f+WrVqtZo+fbrv6tWrQ2xsbL5W49mjRo3qePLkyWTI0OfPn4dujY2lTflESJk1aFVfjNbmzf5ZDcCUZzRq1KjNrVu3lj99+vQI5dq1a4vwX0uI0U7ksGHDnE+dOpWSmJjowWflZGf3uXfvXhccW3wwhHA4yRGMmmTwWSA4WiFD+Z4zZ04//GcNaWUoMYMHD/6WmpGSkuIp7udzbHbn50ciBEPtq6hqKy2LhzEexTG+2kNbasejR492KURcvXp11e3bt/9A3/9f1PMbAilALYhzlSYRERFOJGPKlCleOmTY4tguKirK/cmTJ+PPnz9fsywwVPWdIKTHtm3bVHf60tPTg0jG48eP90+bOnUACt3ZunXrviBlB46/hzgKTWEX9k5CwsLCHEnGtGnTfIqQ0Q6/SYr1unXrAl++fMmRoXY/IKMpwyGlAWLM/7t37x5NQhDRTcX97O+7oz/pA5uyw8bKqhd+dxRgsusqkZCePXs6gIwfZsyY0bUYMuwEqQXdH2xUJLS9mWYZASFh8+fPdzMG8NLuOXLkSAIIOeTasSNbNY1ucNMmTcLQtRxydXWlHXGF2EPalPSsoKCg9iRj9uzZfiWQQbvBcErBIAH2yY110iQhDKEzoloasMb+j5HQ1OvXr/+O+ztBCGhAZmbmD9QaXy8vDq3/AWkvWvhbGuLv729//PjxxHnz5gXqQ4ZSThElbqI5UjifwRC6sYCXdh80JBkyA9dRA33r16nTFcPTHBJSu3ZtkuECoXFnK39jGOzh4WF37Nix+IULF3YzhAyWiXVi3TRFCEMOnAgyZ5wKA4Wo/fv3/wxg2DV5wpb8QjLu3r27Bb+/FZrjhG8bXUJIBoiMX7x4MQ00DT6HtjTcNOC0GW90U0UbBuskJrm0E1ZBgb9R2wksCsykSZN8zpw5s4CaMHf27DHK8PfAgQO/CpJo6DvoEuLs7Nz26NGjk5YsWcIWbjAZShlEAJLap40PDV9cXBxbaanjfxOuaXPnzp3NGzZsmERDrhACf4Jge0PoT7AMBeEUe3t7m8OHD09csWJFX0GGlSGaoVtO1k0zxl0EEM3aXREcW3wwxF2v66mjK6LGkAj6PRx9scuyZozr0KFDsYx3mUoG3610W5oIPKLlfInxOh01s2nHmDFjXGAr3iADo5/sli1a0C7QUHOU1RnSvnnz5rboxmKysrI4XGU3Rc1oC9HLZpRUD9aRdS33fRYK6cz5BHMRwqAfQiY7dDUDZPzR2cMjAu9kd0WhhnRq2LChw549e2LWrl3bvwQybHC+0M8wpMycQkBd6XyW7w8KGWSuRIXiyKD9sGrVKhReej9IKNBhRNnrrzVrOmdnZ8esX7+e2kpPu6hmGE0GiWMdWdfyzQZKh0JGMFHNkNamz7V+fn7t79+/v01XMzDrOBNBxa1dfHyiQEY4ScGzun5Wtarrlk2bqBlDzEEGy8s6sq5aICSK2YP6gKzvNTCiLW/cuLFEIQNacRBO3Xh2TZs2blzwCz4ggwY74NOqVT3WrFkTuy4ra6i5yGC53d3dbUBIlBYIGaeb3qkv6O+6Dmk6wxUyYD/2p6aksOUHUCNiY2OT8vPyluF3dwsLi87Lly2LRTc1onLlyrQNqnZTumXEYKE1CPl3uSeESc9qkKA8g1O0IGG3IOQworLD8J8fI7vUirlpaT89ePBgH4KEKy9durT59OnTC3v37s20IgYVldEUR1S0GTxn9Gxi0XqJupZvTtQmhOFxRTt27tz5I2rvC+n1ZfPmAxCPWsX/nj17duTFixeXMP99Gb+PPnz48MCqVav+hesY7TULGTVq1GgJDYku32ygdCikql3WiBEjOhJ0GPQcjJzogXevXLFiP2jDJoUoEFFIBuzLUUxaHaPAKx8nSGG8SjXNoKZY4YO6jtECIaoadYxmOhF42JEUVL5g3mPWrFlJhYkNV69uQXzq9115efOC/P2HbtqwIR1kFJCCruwgh7+4R5ljV81Z9fHxsQUhw7VAiKrDXnjlBYQkJSRwiOkH6QYClvLcxYsX//fb/PmJDu3a9blw4cJW2JT+kIGpyck/kRBqi5ubG712BgJJSqnTuUXtREm/tTTsVdUxDAkJ6UDwRw4fzqAgNcQXhjsT3dRFxKZ+xJC4K0gIRgJCzt/q1x+K4wj6JPDQC+yLr68vHUPOmdCeFC7u0Rf4kq7TkmPozJVLplZYub9OnTpWHOrOSUsbgXNeSI7ywExfBoz4lVaWll3wO4gjruytWzPQaifgmp44FzhrxgxO8R7xdHfnrCUHAgzFU0tU6ba0FDpRO7jYEq09CRqQCTA94IOMhgc+GZHereiu4kBGT5Lwn5kz4zdv3jwTx8yp6oTfo0iIva0tHUZqFm2JDUQV466Z4KIZwu9f1atd2555Vztzc9OXLl06uV69eh3t7Oy80ErTly1b9muN6tUDegQF9cLIK0sA78iJKozM8qtYWFA7GIonIfRLTCZEU+F3VJhDX7UnqKyR8TECE1L74YUnVapUiY6fC8S7Vo0a3eChUyu+AwG5zZo164QgZF/4Irtg3Blyp/1gKJ73qEKIpiaoBCGqTuEisSBk48aNSa1atPBEd5V07ty5VYsRy3JycGDGCJPiXCwtLV2uXLmyHCmfBTJ69GjaMdoNZqUw2YLHbyU84JzBNkWLU7iqJTkgtScYs31xbdu2ZSYio8huDerW9YGRH4tkuTTMq2cyXIJhbwYCkGsOHjzITBRqA6dvOV9BQigOEJONuiaTHISWmJwGxMwQZojAl2D4g2DSn2CKD7shgkzgeY75V+2Ql9sP2qHkailEkBRqBzNL3pnFqI+2aDINSBBiUqJcRkZGN+ZOCTLoP7D/J/AEV9EAagxbPs+3r1apUgfkZuU1btCAhpxZJySF15AMRn5Ndgw1mygnSAlj+qU+LU/3mrlz5wYwq9Db25tAkwz2/cyZoqa0QwaJM5Kt+8fExHCquIAMQYwTNOq32Oho+h60GySO9/EZJpOBlFNXzWSbkICiHyYmM0HZEEKYZ4slBYmBgYFs+UXJsMVcVA/dZQiYNYwTGkAtsEMyQ0zmkiUMKrJro90hmSaTwTpoPtlaaAmXI/jrQwqChl2Y/BwcHEwg3yID56zQJW3Rncbl7CFGWEq39DXCKcNzcnIShXYoub0m+x6sA5dWFNfwNHVOWbCTkJDwzrXoaPneXKMRGhpKcIsjwxpZ7fa6ZCjHCC4WpPxArJHxMmHlypWcWuVzeM4GYpIxZ9nFxgKfawr8kgoLUt65pA1kdCYZ4eHhNNjFkiG6nZbIxyqcB2HGCTRiOv7zg7g5ODi4ICN+uaOjI71yahm7PQ4GTCJEbCjwYe3yAFKKXfSZmpr6PcmIjIykBpVERuF6wfj4eF947NlYKZUbER4+mpFdRHnD02bNmgA/JGvy5MmMbdGeqEKIcAI5MfZhfbgsGhKqGwlGV+DOFa9c+aoPGbiGXrV1tWrVnPv27RuJLZfiJ06cmJoQF5c4dMiQoXXr1qUh56iKoXYSwi6LRt0oDWFZ0ZC4YxBX4H54H1TsU1RwEIxuIOJBbtSMkSNHuhhABgmhP0FnkDm8wSIXi5NQjGcpdoNE8BplSZrBoyyWkWVlmT88JnRqJNaORKLL+ZnTs6L10gtX/Ax2N+9a1szWzmuoVTTmihPI34pPwm9qiaIdBo2yxNYa3MlBO2tATGk1qGhVrGgN2bFjR28BPgEuXPEqSCoJRLZ2pvPwHmoBBwJK98RzipBUgx3Cj27zGYVIzGtU4Q4JSO0ZNGzIEEZsS9MM3agsSVHydQm8rrBLI2H8X++uSmd7Ju+PbnsmQQq6/wqVD+3da4fZvxj4DhwdFY6mcKxPWJxapJDDexUSDOqiuIEZsxA5PDdF87V+Lzdz4eilMnJx/4Jkt24ITbzXLf64ZJvr6EEEDfhHv8WfsrsONYXyidgEM4wRVXMtaaDm6WyCyR3kmmq9ZZu9/IIYbhMbi61de6i4TWwPPhMit4k1hkVuegzw3tpImS187Nixjlj8b8uUTs7iUXjMc/yP18iNlI1BXc97RDZLwVbjOOYWfNxOfBjza5n0LITHPBchrnHGsdxqXE+M5WUSAYmAREAiIBGQCEgEJAISAYmAREAiIBGQCEgEJAISAYmAREAiIBGQCEgEJAISAYmAREAiIBEozwj8H/vpbLnJxJ/cAAAAAElFTkSuQmCC);
    background-size: 100% 100%;
    z-index: 1000000;
    // right: 0;
    // top: 0;

    &.on {
      background-image: url(data:image/png;base64,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);
      animation: player 2s linear infinite;
    }
  }
</style>

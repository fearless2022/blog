## 安装

* ```python
  pip install alive-progress
  ```

## 样式

* bar

* ```python
     classic [========================================]
    classic2 [########################################]
      smooth |████████████████████████████████████████|
      blocks |▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉|
     bubbles <●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●>
     circles <●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●>
      hollow <❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒>
     squares <■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■>
       solid <■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■>
      checks |✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓✓|
     filling |████████████████████████████████████████|
  ```

* spinners

* ```python
                     |\|       classic       |\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\|
                  | *  |        stars        |                             ********** |
                     |↘|        arrow        |↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘↘|
                   |↘↓↙|       arrows        |↘↓↙←↖↑↗→↘↓↙←↖↑↗→↘↓↙←↖↑↗→↘↓↙←↖↑↗→↘↓↙←↖↑↗→|
                     |▂|      vertical       |▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂▂|
                   |▂▂▄|        waves        |▂▂▄▆█▆▄▂▂▄▆█▆▄▂▂▄▆█▆▄▂▂▄▆█▆▄▂▂▄▆█▆▄▂▂▄▆█|
                   |▂▅▆|       waves2        |▂▅▆▁▆▅▂▇▄▃█▃▄▇▂▅▆▁▆▅▂▇▄▃█▃▄▇▂▅▆▁▆▅▂▇▄▃█▃|
                   |▂▇▂|       waves3        |▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇▂▇|
                     |▊|     horizontal      |▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊▊|
                     |⡀|        dots         |⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀⡀|
                     |⢿|    dots_reverse     |⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿⢿|
                 |⡀⠄⠂⠁⠈|     dots_waves      |⡀⠄⠂⠁⠈⠐⠠⢀⡀⠄⠂⠁⠈⠐⠠⢀⡀⠄⠂⠁⠈⠐⠠⢀⡀⠄⠂⠁⠈⠐⠠⢀⡀⠄⠂⠁⠈⠐⠠⢀|
                 |⡀⠂⠈⠠⡀|     dots_waves2     |⡀⠂⠈⠠⡀⠂⠈⠠⡀⠂⠈⠠⡀⠂⠈⠠⡀⠂⠈⠠⡀⠂⠈⠠⡀⠂⠈⠠⡀⠂⠈⠠⡀⠂⠈⠠⡀⠂⠈⠠|
                   |●∙∙|   ball_scrolling    |∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙●∙∙∙∙∙∙∙∙∙∙∙∙|
                   |●∙∙|   balls_scrolling   |∙∙∙●●●●●●●●●●●●●∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙∙|
              | ●      |    ball_bouncing    |         ●                              |
              | ●      |   balls_bouncing    | ●●●●●                                  |
                   |...|     dots_recur      |                             ...........|
                  |=   |      bar_recur      |                                       =|
                 |►   ►|       pointer       |►►►►►                        ►►►►►►►►►►►|
                |←←←   |    arrows_recur     |                               ←←←←←←←←←|
                |   ◀◀ |      triangles      |               ▶▶▶▶▶▶▶▶▶▶▶▶▶            |
          |◀◀◀      ▶▶▶|     triangles2      |◀◀◀      ▶▶▶◁◁◁      ▷▷▷◀◀◀      ▶▶▶◁◁◁ |
              | <<<    |      brackets       |                   >>>>>>>>>>>>>>>      |
            | ○○○○○    |    balls_filling    |           ○○○○○○○○○○○○○○○○○○○○         |
            |         ♩|        notes        |                           ♫♫♫♫♫♫♫♫♫♫♫♫♫|
            |   ♬♬♬♬   |       notes2        |                     ♪♪♪♪♪♪♪♪♪♪♪♪♪♪♪♪   |
            |♬♬♬      ♫|   notes_scrolling   |♫♫♫♫♫                        ♪♪♪♪♪♪♪♪♪♪♪|
          |     ><     |   arrows_incoming   |>>>                                  <<<|
          |<          >|   arrows_outgoing   |                 <<<>>>                 |
    |      >>------>   |     real_arrow      |           >>------>                    |
       |'>       ><((((|        fish         |                             ><(((('>   |
      |´¯`·.¸¸.·´¯`·.¸>|        fish2        |                                      ¸.|
    |         ><(((('> |    fish_bouncing    |     ><(((('>                           |
    |    ><>           |       fishes        |                      <><  <><    <><   |
        | please wait..|  message_scrolling  |                         please wait... |
       |           wait|  message_bouncing   | wait                                   |
       |han anticipated|    long_message     |                     well, this is takin|
        |–––––––––––––•|        pulse        |–––––––––––––•–––––––––––––•––––––––––––|
  ```

## 使用

* ```python
  from alive_progress import showtime, show_bars, show_spinners, alive_bar
  import time
  
  # 请注意，如果无法正常显示动画则尝试在 alive_bar 中加上 force_tty=True 参数
  # 需要注意的是 alive-progress 并不像 tqdm 等进度条库一样会自动更新，只有我们程序调用了 bar 才会让进度条 +1
  def loading():
          with alive_bar(total=100, title="loading", bar="smooth", spinner="fish_bouncing", force_tty=True) as bar:
          for i in range(100):
              time.sleep(0.05)
              bar()
  
  
  if __name__ == '__main__':
      loading()
  
  ```
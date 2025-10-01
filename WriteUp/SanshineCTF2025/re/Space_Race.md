# SanshineCTF2025　Space Race

## 問題文
NASA was so excited to send their new unmanned rover out to pick up some stranded astronauts that they forgot to actually write the software to control it. Can you take a look at the onboard ECU software and see if you can find a way to establish communications?

If worse comes to worst, maybe they CAN BUS the astronauts back....

the server binary has been updated to not be stripped

nc chal.sunshinectf.games (省略)

NASAは遭難した宇宙飛行士を救助するため新型無人探査車を送り込むことに興奮しすぎて、実際に制御するソフトウェアを書くのを忘れてしまった。搭載されているECUソフトウェアを確認し、通信確立の方法を見つけられるか見てくれないか？

最悪の場合、宇宙飛行士をCANバスで引き戻せるかもしれない…

サーバーバイナリはストリップ処理されないよう更新済み

nc chal.sunshinectf.games (省略)
サーバーバイナリはストリップされないように更新されました

この問題にはclient.pyとDockerfileとserverが渡されている。
client.pyの中身は以下の通り

```
import sys, socket, json, threading, queue, time
import pygame

WIDTH, HEIGHT = 900, 600
BG = (8, 10, 16)
FG = (230, 235, 240)
TRACK_CLR = (40, 40, 48)
FINISH_CLR = (220, 220, 220)
ROVER_CLR = (120, 200, 255)
OBST_CLR = (255, 120, 120)
PX_PER_WU = 2.2

class Net:
    def __init__(self, host, port):
        self.sock = socket.create_connection((host, port))
        self.r = self.sock.makefile('r', buffering=1, encoding='utf-8', newline='\n')
        self.q = queue.Queue()
        self.alive = True
        threading.Thread(target=self.reader, daemon=True).start()
    def reader(self):
        try:
            for line in self.r:
                line = line.strip()
                if not line:
                    continue
                try:
                    self.q.put(json.loads(line))
                except Exception:
                    pass
        except Exception:
            pass
        self.alive = False

def world_to_screen_x(x_wu, half_width_wu, rover_x=0.0):
    left = WIDTH * 0.2
    right = WIDTH * 0.8
    x_rel = float(x_wu) - float(rover_x)
    t = (x_rel + half_width_wu) / (2 * half_width_wu)
    return int(left + t * (right - left))

def main():
    if len(sys.argv) != 3:
        print(f"usage: python {sys.argv[0]} HOST PORT")
        sys.exit(1)
    host, port = sys.argv[1], int(sys.argv[2])

    net = Net(host, port)
    pygame.init()
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Space Race: Rover")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont(None, 20)
    big = pygame.font.SysFont(None, 48)

    latest = None
    flag = None
    flag_time = None

    while True:
        try:
            while True:
                obj = net.q.get_nowait()
                if obj.get("t") == "telemetry":
                    latest = obj
                    if obj.get("flag") is not None and flag is None:
                        flag = obj["flag"]
                        flag_time = time.time()
        except queue.Empty:
            pass
        '''
        
        TODO: Implement the controls for the rover client!

        {"t":"can","frame":"0123456789abcdef"}
        
        '''
        for e in pygame.event.get():
            if e.type == pygame.QUIT:
                pygame.quit(); return
            if e.type == pygame.KEYDOWN:
                if e.key in (pygame.K_ESCAPE, pygame.K_q):
                    pygame.quit(); return
                elif e.key == pygame.K_UP:
                    pass
                elif e.key == pygame.K_DOWN:
                    pass
                elif e.key == pygame.K_LEFT:
                    pass
                elif e.key == pygame.K_RIGHT:
                    pass
                elif e.key == pygame.K_b:
                    pass
                elif e.key == pygame.K_s:
                    pass
                elif e.key == pygame.K_r:
                    pass

        screen.fill(BG)

        if latest:
            track = latest.get("track", {})
            half_w = float(track.get("half_width", 60.0))
            rover_x = float(latest.get("x", 0.0))
            tlx = world_to_screen_x(-half_w, half_w, rover_x)
            trx = world_to_screen_x(+half_w, half_w, rover_x)
            track_px_w = max(1, trx - tlx)
            pygame.draw.rect(screen, TRACK_CLR, (tlx, 0, track_px_w, HEIGHT), border_radius=12)

            s = float(latest.get("s", 0.0))
            length = float(track.get("length", 1.0))
            progress = s / max(1.0, length)
            if progress > 0.85:
                for y in range(0, HEIGHT, 14):
                    pygame.draw.line(screen, FINISH_CLR, (trx, y), (trx, y+7), 3)

            obs = latest.get("obstacles", [])
            if obs is not None:
                for ob in obs:
                    try:
                        dy = float(ob["dy"])
                        if dy < 0:
                            continue
                        y = int(HEIGHT/2 - dy * PX_PER_WU)
                        x = world_to_screen_x(float(ob["x"]), half_w, rover_x)
                        w_px = max(6, int((float(ob["w"]) / (2 * half_w)) * track_px_w))
                        rect = pygame.Rect(x - w_px//2, y - 10, w_px, 20)
                        pygame.draw.rect(screen, OBST_CLR, rect, border_radius=4)
                    except (KeyError, ValueError, TypeError):
                        continue

            rover_rect = pygame.Rect(WIDTH//2 - 16, HEIGHT//2 + 18, 32, 44)
            pygame.draw.rect(screen, ROVER_CLR, rover_rect, border_radius=6)
            pygame.draw.rect(screen, FG, rover_rect, 2, border_radius=6)

            hud = [
                f"Status: {latest.get('status','')}",
                f"Dist: {s:.1f}/{length:.0f} wu",
                f"Lateral x: {rover_x:.1f} wu",
                f"Speed: {float(latest.get('vel',0.0)):.2f} wu/s",
                f"Throttle: {latest.get('throttle_pct',0)}%  Steer: {latest.get('steer_pct',0)}%",
                f"{str(latest.get('msg',''))[:64]}",
            ]
            for i, line in enumerate(hud):
                img = font.render(line, True, FG)
                screen.blit(img, (12, 10 + i*18))

        if flag:
            img = big.render(flag, True, (255, 240, 120))
            screen.blit(img, img.get_rect(center=(WIDTH//2, HEIGHT//2 - 80)))
            if time.time() - flag_time > 6:
                pygame.quit(); return

        pygame.display.flip()
        clock.tick(60)

if __name__ == "__main__":
    main()
```

serverファイルはlinux実行可能ファイルっぽい
```
└─$ file server
server: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, Go BuildID=VjjYTu_mtcyuxIRGYazD/psE3stoJI4SCXcPpgJlK/bwZQQmvIXHuvLw_0F3vN/2JL02q9_X-wovZ1WyWEq, not stripped
```
実行すると次のようになる
```
{"t":"telemetry","time":1759199969.6859658,"s":0,"x":0,"vel":0,"heading_deg":0,"throttle_pct":0,"steer_pct":0,"status":"ok","track":{"length":1000,"half_width":60,"unit":"wu"},"obstacles":[{"dy":140,"x":20.411417897604274,"w":28.0430452215625}],"msg":"Rover link up. Implement controls to move!","flag":null}
{"t":"telemetry","time":1759199969.7694519,"s":0,"x":0,"vel":0,"heading_deg":0,"throttle_pct":0,"steer_pct":0,"status":"ok","track":{"length":1000,"half_width":60,"unit":"wu"},"obstacles":[{"dy":140,"x":20.411417897604274,"w":28.0430452215625}],"msg":"Rover link up. Implement controls to move!","flag":null}
```
cliant.pyはserverからのこの出力を受け取って画面に表示するプログラム
TODOを見ると自分で操作する部分はできていないのでその部分を作成する

claudeに投げたがどのような信号を送ればいいかわからんとのことだったので、serverファイルをghidraで解析
mainの中身を見る。

```
/* WARNING: Removing unreachable block (ram,0x004ba074) */

void main.main(void)

{
  int iVar1;
  char cVar2;
  undefined8 uVar3;
  long lVar4;
  undefined8 *puVar5;
  ulong in_RCX;
  undefined8 uVar6;
  int *piVar7;
  ulong uVar8;
  long unaff_RBX;
  undefined1 *puVar9;
  undefined8 unaff_RBP;
  ushort *puVar10;
  runtime.itab *prVar11;
  ushort uVar12;
  ulong uVar13;
  char *pcVar14;
  long unaff_R14;
  undefined8 in_XMM15_Qa;
  undefined8 in_XMM15_Qb;
  undefined1 auVar15 [16];
  
  do {
    puVar9 = (undefined1 *)register0x00000020;
    if (*(undefined1 **)(unaff_R14 + 0x10) < (undefined1 *)((long)register0x00000020 + -0x138)) {
      puVar9 = (undefined1 *)((long)register0x00000020 + -0x1b8);
      *(undefined8 *)((long)register0x00000020 + -8) = unaff_RBP;
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9aae;
      uVar3 = time.Now();
      *(undefined8 *)((long)register0x00000020 + -0xd0) = uVar3;
      *(long *)((long)register0x00000020 + -200) = unaff_RBX;
      *(ulong *)((long)register0x00000020 + -0xc0) = in_RCX;
      if (*(long *)((long)register0x00000020 + -0xd0) < 0) {
        unaff_RBX = ((ulong)(*(long *)((long)register0x00000020 + -0xd0) << 1) >> 0x1f) +
                    0xdd7b17f80;
      }
      uVar6 = 0xfffffff1886e0900;
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9b05;
      uVar3 = main.makeCourse();
      *(undefined8 *)((long)register0x00000020 + -0x108) = uVar3;
      *(long *)((long)register0x00000020 + -0x160) = unaff_RBX;
      *(undefined8 *)((long)register0x00000020 + -0x158) = uVar6;
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9b25;
      lVar4 = runtime.newobject();
      *(long *)((long)register0x00000020 + -0x120) = lVar4;
      *(undefined8 *)(lVar4 + 0x40) = 2;
      *(undefined2 **)(lVar4 + 0x38) = &DAT_004d9c9a;
      *(undefined8 *)(lVar4 + 0x50) = 0x2a;
      *(undefined **)(lVar4 + 0x48) = &DAT_004e14c3;
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9b5f;
      puVar5 = (undefined8 *)runtime.newobject();
      *puVar5 = main.main.func1;
      if (runtime.writeBarrier._0_4_ == 0) {
        puVar5[1] = *(undefined8 *)((long)register0x00000020 + -0x120);
      }
      else {
        *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9b93;
        puVar5 = (undefined8 *)runtime.gcWriteBarrierCX(puVar5 + 1);
      }
      puVar5[3] = *(undefined8 *)((long)register0x00000020 + -0x160);
      puVar5[4] = *(undefined8 *)((long)register0x00000020 + -0x158);
      if (runtime.writeBarrier._0_4_ == 0) {
        puVar5[2] = *(undefined8 *)((long)register0x00000020 + -0x108);
      }
      else {
        *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9bcd;
        runtime.gcWriteBarrierSI(puVar5 + 2,*(undefined8 *)((long)register0x00000020 + -0x108));
      }
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9bd2;
      runtime.newproc();
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9bde;
      puVar5 = (undefined8 *)runtime.newobject();
      *puVar5 = main.main.func2;
      if (runtime.writeBarrier._0_4_ == 0) {
        puVar5[1] = *(undefined8 *)((long)register0x00000020 + -0x120);
      }
      else {
        *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9c10;
        puVar5 = (undefined8 *)runtime.gcWriteBarrierCX(puVar5 + 1);
      }
      puVar5[3] = *(undefined8 *)((long)register0x00000020 + -0x160);
      puVar5[4] = *(undefined8 *)((long)register0x00000020 + -0x158);
      if (runtime.writeBarrier._0_4_ == 0) {
        puVar5[2] = *(undefined8 *)((long)register0x00000020 + -0x108);
      }
      else {
        *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9c4a;
        runtime.gcWriteBarrierDX(puVar5 + 2);
      }
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9c4f;
      runtime.newproc();
      *(os.File **)((long)register0x00000020 + -0xf0) = os.Stdin;
      *(undefined8 *)((long)register0x00000020 + -0x60) = in_XMM15_Qa;
      *(undefined8 *)((long)register0x00000020 + -0x58) = in_XMM15_Qb;
      *(undefined1 **)((long)register0x00000020 + -0x1c8) =
           (undefined1 *)((long)register0x00000020 + -8);
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9c8f;
      FUN_0045e610((undefined1 *)((long)register0x00000020 + -0x88));
      uVar3 = *(undefined8 *)((long)register0x00000020 + -0x1c8);
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9ca7;
      runtime.makeslice();
      *(undefined8 *)((long)register0x00000020 + -0xb8) = in_XMM15_Qa;
      *(undefined8 *)((long)register0x00000020 + -0xb0) = in_XMM15_Qb;
      *(undefined8 *)((long)register0x00000020 + -0x1c8) = uVar3;
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9ccf;
      uVar3 = FUN_0045e610((undefined1 *)((long)register0x00000020 + -0xe0));
      *(undefined8 *)((long)register0x00000020 + -0xb8) = uVar3;
      *(undefined8 *)((long)register0x00000020 + -0xb0) = 0x1000;
      *(undefined8 *)((long)register0x00000020 + -0xa8) = 0x1000;
      *(undefined1 **)((long)register0x00000020 + -0xa0) = go.itab.*os.File,io.Reader;
      *(undefined8 *)((long)register0x00000020 + -0x98) =
           *(undefined8 *)((long)register0x00000020 + -0xf0);
      *(undefined8 *)((long)register0x00000020 + -0x70) = 0xffffffffffffffff;
      *(undefined8 *)((long)register0x00000020 + -0x68) = 0xffffffffffffffff;
      *(undefined8 *)((long)register0x00000020 + -0x60) =
           *(undefined8 *)((long)register0x00000020 + -0xb8);
      prVar11 = (runtime.itab *)((long)register0x00000020 + -0x58);
      *(undefined8 *)((long)register0x00000020 + -0x1c8) =
           *(undefined8 *)((long)register0x00000020 + -0x1c8);
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9d59;
      FUN_0045e97a(prVar11,(undefined1 *)((long)register0x00000020 + -0xb0));
      unaff_RBP = *(undefined8 *)((long)register0x00000020 + -0x1c8);
      piVar7 = *(int **)((long)register0x00000020 + -0x120);
      *(int **)((long)register0x00000020 + -0xf8) = piVar7 + 0xe;
      *(int **)((long)register0x00000020 + -0x100) = piVar7 + 0x12;
LAB_004b9d7d:
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9d8f;
      uVar3 = bufio.(*Reader).ReadBytes();
      if (prVar11 != (runtime.itab *)0x0) {
        if (io.EOF.tab == prVar11) {
          *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9db8;
          cVar2 = runtime.ifaceeq();
          if (cVar2 != '\0') {
            *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9dd2;
            time.Sleep();
            piVar7 = *(int **)((long)register0x00000020 + -0x120);
            prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
            goto LAB_004b9d7d;
          }
        }
        return;
      }
      *(int **)((long)register0x00000020 + -0x170) = piVar7;
      *(undefined8 *)((long)register0x00000020 + -0x178) = 10;
      *(undefined8 *)((long)register0x00000020 + -0x118) = uVar3;
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9e17;
      puVar5 = (undefined8 *)runtime.newobject();
      *(undefined8 **)((long)register0x00000020 + -0xe8) = puVar5;
      *puVar5 = 0;
      puVar5[2] = 0;
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9e45;
      main.trim();
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9e59;
      lVar4 = encoding/json.Unmarshal
                        (&DAT_004c1ce0,*(undefined8 *)((long)register0x00000020 + -0xe8));
      if (lVar4 != 0) {
        piVar7 = *(int **)((long)register0x00000020 + -0x120);
        prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
        goto LAB_004b9d7d;
      }
      puVar5 = *(undefined8 **)((long)register0x00000020 + -0xe8);
      if (((puVar5[1] != 3) || (*(short *)*puVar5 != 0x6163)) ||
         ((char)((short *)*puVar5)[1] != 'n')) {
LAB_004b9eb5:
        piVar7 = *(int **)((long)register0x00000020 + -0x120);
        prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
        goto LAB_004b9d7d;
      }
      lVar4 = puVar5[3];
      unaff_RBX = puVar5[2];
      if (lVar4 == 0) goto LAB_004b9eb5;
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9eea;
      uVar3 = runtime.stringtoslicebyte();
      *(undefined8 *)((long)register0x00000020 + -0x128) = uVar3;
      *(long *)((long)register0x00000020 + -0x180) = lVar4;
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9f05;
      in_RCX = encoding/hex.Decode(uVar3,unaff_RBX);
      uVar8 = *(ulong *)((long)register0x00000020 + -0x180);
      if (uVar8 < in_RCX) goto LAB_004ba4b4;
      if ((unaff_RBX != 0) || ((long)in_RCX < 3)) {
        piVar7 = *(int **)((long)register0x00000020 + -0x120);
        prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
        goto LAB_004b9d7d;
      }
      puVar10 = *(ushort **)((long)register0x00000020 + -0x128);
      uVar12 = *puVar10;
      uVar13 = (ulong)((byte)puVar10[1] & 0xf);
      if ((8 < uVar13) || ((long)in_RCX < (long)(uVar13 + 3))) {
        piVar7 = *(int **)((long)register0x00000020 + -0x120);
        prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
        goto LAB_004b9d7d;
      }
      if (2 < uVar13 + 3) {
        piVar7 = *(int **)((long)register0x00000020 + -0x120);
        LOCK();
        iVar1 = *piVar7;
        if (iVar1 == 0) {
          *piVar7 = 1;
        }
        UNLOCK();
        uVar8 = (ulong)((uint)((long)-(uVar8 - 3) >> 0x3f) & 3);
        pcVar14 = (char *)((long)puVar10 + uVar8);
        if (iVar1 != 0) {
          *(ulong *)((long)register0x00000020 + -0x168) = uVar13;
          *(ulong *)((long)register0x00000020 + -0x150) = uVar8;
          *(char **)((long)register0x00000020 + -0x110) = pcVar14;
          *(ushort *)((long)register0x00000020 + -0x182) = uVar12;
          *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4b9ffb;
          sync.(*Mutex).lockSlow();
          piVar7 = *(int **)((long)register0x00000020 + -0x120);
          uVar8 = *(ulong *)((long)register0x00000020 + -0x150);
          puVar10 = *(ushort **)((long)register0x00000020 + -0x128);
          uVar12 = *(ushort *)((long)register0x00000020 + -0x182);
          uVar13 = *(ulong *)((long)register0x00000020 + -0x168);
          pcVar14 = *(char **)((long)register0x00000020 + -0x110);
        }
        uVar12 = (uVar12 & 7) << 8 | uVar12 >> 8;
        if (uVar12 < 0x203) {
          if (uVar12 == 0x201) {
            if ((long)uVar13 < 1) {
              prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
            }
            else {
              uVar8 = (ulong)*(byte *)((long)puVar10 + uVar8);
              if (100 < uVar8) {
                uVar8 = 100;
              }
              *(ulong *)(piVar7 + 10) = uVar8;
              *(undefined8 *)((long)register0x00000020 + -0xe0) = in_XMM15_Qa;
              *(undefined8 *)((long)register0x00000020 + -0xd8) = in_XMM15_Qb;
              *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba099;
              auVar15 = runtime.convT64();
              *(undefined **)((long)register0x00000020 + -0xe0) = &DAT_004c4760;
              *(long *)((long)register0x00000020 + -0xd8) = auVar15._0_8_;
              *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba0d1;
              uVar3 = fmt.Sprintf(1,1,auVar15._8_8_,(undefined1 *)((long)register0x00000020 + -0x e0)
                                 );
              piVar7 = *(int **)((long)register0x00000020 + -0x120);
              piVar7[0x14] = 0xd;
              piVar7[0x15] = 0;
              if (runtime.writeBarrier._0_4_ == 0) {
                *(undefined8 *)(piVar7 + 0x12) = uVar3;
                prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
              }
              else {
                prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
                *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba105;
                runtime.gcWriteBarrier();
              }
            }
          }
          else if (uVar12 == 0x202) {
            if ((long)uVar13 < 1) {
              prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
            }
            else {
              lVar4 = (long)*pcVar14;
              if (lVar4 < -100) {
                lVar4 = -100;
              }
              else if (100 < lVar4) {
                lVar4 = 100;
              }
              *(long *)(piVar7 + 0xc) = lVar4;
              *(undefined8 *)((long)register0x00000020 + -0xe0) = in_XMM15_Qa;
              *(undefined8 *)((long)register0x00000020 + -0xd8) = in_XMM15_Qb;
              *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba174;
              auVar15 = runtime.convT64();
              *(undefined **)((long)register0x00000020 + -0xe0) = &DAT_004c4760;
              *(long *)((long)register0x00000020 + -0xd8) = auVar15._0_8_;
              *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba1ac;
              uVar3 = fmt.Sprintf(1,1,auVar15._8_8_,(undefined1 *)((long)register0x00000020 + -0x e0)
                                 );
              piVar7 = *(int **)((long)register0x00000020 + -0x120);
              piVar7[0x14] = 10;
              piVar7[0x15] = 0;
              if (runtime.writeBarrier._0_4_ == 0) {
                *(undefined8 *)(piVar7 + 0x12) = uVar3;
                prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
              }
              else {
                prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
                *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba1dd;
                runtime.gcWriteBarrier();
              }
            }
          }
          else {
LAB_004ba3c5:
            *(undefined8 *)((long)register0x00000020 + -0xe0) = in_XMM15_Qa;
            *(undefined8 *)((long)register0x00000020 + -0xd8) = in_XMM15_Qb;
            *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba3d6;
            auVar15 = runtime.convT16();
            *(undefined **)((long)register0x00000020 + -0xe0) = &DAT_004c4e20;
            *(long *)((long)register0x00000020 + -0xd8) = auVar15._0_8_;
            *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba40e;
            uVar3 = fmt.Sprintf(1,1,auVar15._8_8_,(undefined1 *)((long)register0x00000020 + -0xe0 ));
            piVar7 = *(int **)((long)register0x00000020 + -0x120);
            piVar7[0x14] = 0x12;
            piVar7[0x15] = 0;
            if (runtime.writeBarrier._0_4_ == 0) {
              *(undefined8 *)(piVar7 + 0x12) = uVar3;
              prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
            }
            else {
              prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
              *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba43e;
              runtime.gcWriteBarrier();
            }
          }
        }
        else if (uVar12 == 0x203) {
          *(double *)(piVar7 + 6) = *(double *)(piVar7 + 6) * 0.65;
          piVar7[0x14] = 6;
          piVar7[0x15] = 0;
          if (runtime.writeBarrier._0_4_ == 0) {
            *(undefined **)(piVar7 + 0x12) = &DAT_004da073;
            prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
          }
          else {
            prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
            *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba24f;
            runtime.gcWriteBarrierDX(prVar11,puVar10,&DAT_004da073);
          }
        }
        else if (uVar12 == 0x204) {
          piVar7[0xc] = 0;
          piVar7[0xd] = 0;
          *(double *)(piVar7 + 8) = *(double *)(piVar7 + 8) * 0.9;
          piVar7[0x14] = 0xe;
          piVar7[0x15] = 0;
          if (runtime.writeBarrier._0_4_ == 0) {
            *(undefined **)(piVar7 + 0x12) = &DAT_004db64a;
            prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
          }
          else {
            prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
            *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba2c5;
            runtime.gcWriteBarrierDX(prVar11,puVar10,&DAT_004db64a);
          }
        }
        else {
          if (uVar12 != 0x205) goto LAB_004ba3c5;
          if ((*(long *)(piVar7 + 0x10) == 2) && (**(short **)(piVar7 + 0xe) == 0x6b6f)) {
            prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
          }
          else {
            piVar7[2] = 0;
            piVar7[3] = 0;
            piVar7[4] = 0;
            piVar7[5] = 0;
            piVar7[6] = 0;
            piVar7[7] = 0;
            *(undefined8 *)(piVar7 + 10) = in_XMM15_Qa;
            *(undefined8 *)(piVar7 + 0xc) = in_XMM15_Qb;
            piVar7[0x10] = 2;
            piVar7[0x11] = 0;
            if (runtime.writeBarrier._0_4_ == 0) {
              *(undefined2 **)(piVar7 + 0xe) = &DAT_004d9c9a;
            }
            else {
              *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba357;
              runtime.gcWriteBarrierDX
                        (*(undefined8 *)((long)register0x00000020 + -0xf8),puVar10,&DAT_004d9c9a) ;
            }
            piVar7[0x14] = 0xe;
            piVar7[0x15] = 0;
            if (runtime.writeBarrier._0_4_ == 0) {
              *(undefined **)(piVar7 + 0x12) = &DAT_004db62e;
              prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
            }
            else {
              prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
              *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba39c;
              runtime.gcWriteBarrierSI(prVar11,&DAT_004db62e);
            }
          }
        }
        LOCK();
        iVar1 = *piVar7;
        *piVar7 = *piVar7 + -1;
        UNLOCK();
        if (iVar1 != 1) {
          *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba470;
          sync.(*Mutex).unlockSlow();
          piVar7 = *(int **)((long)register0x00000020 + -0x120);
          prVar11 = *(runtime.itab **)((long)register0x00000020 + -0x100);
        }
        goto LAB_004b9d7d;
      }
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba4b4;
      in_RCX = runtime.panicSliceB();
LAB_004ba4b4:
      *(undefined8 *)((long)register0x00000020 + -0x1c0) = 0x4ba4bc;
      runtime.panicSliceAcap();
    }
    *(undefined8 *)(puVar9 + -8) = 0x4ba4c5;
    runtime.morestack_noctxt();
    register0x00000020 = (BADSPACEBASE *)puVar9;
  } while( true );
}

```

重要なのはここら辺
```
uVar12 = (uVar12 & 7) << 8 | uVar12 >> 8;  // バイトスワップ
if (uVar12 < 0x203) {
  if (uVar12 == 0x201) {      // Throttle
  else if (uVar12 == 0x202) { // Steer
}
else if (uVar12 == 0x203) {   // Brake
else if (uVar12 == 0x204) {   // Reset
else if (uVar12 == 0x205) {   // (Special - status check?)
```
ここで
- 0x201 - スロットル（アクセル）
- 0x202 - ステアリング（ハンドル）
- 0x203 - ブレーキ
- 0x204 - リセット
- 0x205 - 特殊（ステータスが"ok"かチェック）
として判定していることがわかる。
よって、0x201を送るには、バイトスワップ後に0x201になる値 = 0x0102を送る必要がある。

そのまま動かしても動かない。リトルエンディアンではなくてビッグエンディアンで送信するとうまくいった。

そのままclaudeに作ってもらった。
なんか右にしか動かなかったがゴールできたのでまあいいや

```
import sys, socket, json, threading, queue, time
import pygame

WIDTH, HEIGHT = 900, 600
BG = (8, 10, 16)
FG = (230, 235, 240)
TRACK_CLR = (40, 40, 48)
FINISH_CLR = (220, 220, 220)
ROVER_CLR = (120, 200, 255)
OBST_CLR = (255, 120, 120)
PX_PER_WU = 2.2

class Net:
    def __init__(self, host, port):
        self.sock = socket.create_connection((host, port))
        self.r = self.sock.makefile('r', buffering=1, encoding='utf-8', newline='\n')
        self.q = queue.Queue()
        self.alive = True
        threading.Thread(target=self.reader, daemon=True).start()
    
    def reader(self):
        try:
            for line in self.r:
                line = line.strip()
                if not line:
                    continue
                print(f"[RECV] {line}")  # Debug: print all received messages
                try:
                    self.q.put(json.loads(line))
                except Exception as e:
                    print(f"[ERROR] Failed to parse JSON: {e}")
        except Exception as e:
            print(f"[ERROR] Reader thread error: {e}")
        self.alive = False
    
    def send_can(self, frame):
        """Send a CAN frame to the server"""
        msg = json.dumps({"t": "can", "frame": frame}) + "\n"
        print(f"[SEND] {msg.strip()}")  # Debug: print what we're sending
        try:
            self.sock.sendall(msg.encode('utf-8'))
        except Exception as e:
            print(f"[ERROR] Failed to send CAN frame: {e}")

def world_to_screen_x(x_wu, half_width_wu, rover_x=0.0):
    left = WIDTH * 0.2
    right = WIDTH * 0.8
    x_rel = float(x_wu) - float(rover_x)
    t = (x_rel + half_width_wu) / (2 * half_width_wu)
    return int(left + t * (right - left))

def build_can_frame(throttle=0, steer=0, brake=0, special=0, reset=0):
    """
    Build a CAN frame for rover control.
    Assuming 16-character hex string format (8 bytes).
    
    Byte layout (hypothesis):
    - Byte 0-1: Command ID
    - Byte 2: Throttle (0-255, signed or unsigned)
    - Byte 3: Steering (-128 to 127 or 0-255)
    - Byte 4: Brake (0 or 1)
    - Byte 5: Special action
    - Byte 6: Reset
    - Byte 7: Checksum or padding
    """
    # Try different command patterns
    # Common CAN IDs: 0x100-0x7FF
    cmd_id = 0x01  # Command type
    
    # Convert values to bytes (0-255 range)
    throttle_byte = max(0, min(255, throttle + 128))  # -128 to 127 -> 0 to 255
    steer_byte = max(0, min(255, steer + 128))  # -128 to 127 -> 0 to 255
    brake_byte = 1 if brake else 0
    special_byte = special
    reset_byte = reset
    
    # Build 8-byte frame
    frame_bytes = [
        cmd_id,
        0x00,  # Padding or sub-command
        throttle_byte,
        steer_byte,
        brake_byte,
        special_byte,
        reset_byte,
        0x00  # Padding
    ]
    
    # Convert to hex string
    return ''.join(f'{b:02x}' for b in frame_bytes)

def main():
    if len(sys.argv) != 3:
        print(f"usage: python {sys.argv[0]} HOST PORT")
        sys.exit(1)
    host, port = sys.argv[1], int(sys.argv[2])

    net = Net(host, port)
    pygame.init()
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Space Race: Rover")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont(None, 20)
    big = pygame.font.SysFont(None, 48)

    latest = None
    flag = None
    flag_time = None
    auto_drive = False  # Auto-drive mode flag
    last_throttle_time = 0

    while True:
        try:
            while True:
                obj = net.q.get_nowait()
                if obj.get("t") == "telemetry":
                    latest = obj
                    if obj.get("flag") is not None and flag is None:
                        flag = obj["flag"]
                        flag_time = time.time()
        except queue.Empty:
            pass
        
        # Implement controls for the rover client
        for e in pygame.event.get():
            if e.type == pygame.QUIT:
                pygame.quit(); return
            if e.type == pygame.KEYDOWN:
                if e.key in (pygame.K_ESCAPE, pygame.K_q):
                    pygame.quit(); return
                elif e.key == pygame.K_UP:
                    # CAN ID 0x0201 (which becomes 0x201 after byte swap in code)
                    # Format: [CAN_ID_high][CAN_ID_low][length][data...]
                    frame = "0201016400000000"  # ID=0x0201, len=1, throttle=100
                    net.send_can(frame)
                    print(f"[UP] Accelerate: {frame}")
                elif e.key == pygame.K_DOWN:
                    # Throttle to 0
                    frame = "0201010000000000"  # ID=0x0201, len=1, throttle=0
                    net.send_can(frame)
                    print(f"[DOWN] Coast: {frame}")
                elif e.key == pygame.K_LEFT:
                    # CAN ID 0x0202 for steer, value -100 to 100
                    # -100 = 0x9C in two's complement (signed byte)
                    frame = "02020164ff000000"  # ID=0x0202, len=1, steer=-1 (slight left)
                    net.send_can(frame)
                    print(f"[LEFT] Steer left (slight): {frame}")
                elif e.key == pygame.K_RIGHT:
                    # Steer right
                    frame = "0202010100000000"  # ID=0x0202, len=1, steer=1 (slight right)
                    net.send_can(frame)
                    print(f"[RIGHT] Steer right (slight): {frame}")
                elif e.key == pygame.K_a:
                    # Hard left
                    frame = "02020164ff000000"  # steer=-1
                    net.send_can(frame)
                    print(f"[A] Hard left: {frame}")
                elif e.key == pygame.K_d:
                    # Hard right
                    frame = "0202010100000000"  # steer=1
                    net.send_can(frame)
                    print(f"[D] Hard right: {frame}")
                elif e.key == pygame.K_b:
                    # CAN ID 0x0203 for brake
                    frame = "0203010100000000"  # ID=0x0203, len=1, brake=1
                    net.send_can(frame)
                    print(f"[BRAKE] Sent: {frame}")
                elif e.key == pygame.K_s:
                    # CAN ID 0x0205 - status/special
                    frame = "0205010100000000"  # ID=0x0205
                    net.send_can(frame)
                    print(f"[SPECIAL] Sent: {frame}")
                elif e.key == pygame.K_r:
                    # CAN ID 0x0204 for reset
                    frame = "0204010100000000"  # ID=0x0204, len=1
                    net.send_can(frame)
                    print(f"[RESET] Sent: {frame}")
                elif e.key == pygame.K_SPACE:
                    # Toggle auto-drive mode
                    auto_drive = not auto_drive
                    if auto_drive:
                        print("[AUTO] Auto-drive ENABLED - will keep accelerating")
                    else:
                        print("[AUTO] Auto-drive DISABLED")
                elif e.key == pygame.K_a:
                    # Send single throttle command for testing
                    frame = "0201016400000000"
                    net.send_can(frame)
                    print(f"[TEST] Single throttle: {frame}")
                elif e.key == pygame.K_t:
                    # Test: send the example from TODO
                    frame = "0123456789abcdef"
                    net.send_can(frame)
                    print(f"[TEST] Sent example frame: {frame}")
                elif e.key == pygame.K_1:
                    # Test various CAN IDs to find valid ones - Extended scan
                    print("\n[SCAN] Starting CAN ID scan...")
                    test_ids = [
                        # Standard CAN IDs for automotive/robotics
                        0x000, 0x001, 0x010, 0x020, 0x030, 0x040, 0x050, 0x060, 0x070,
                        0x080, 0x090, 0x0A0, 0x0B0, 0x0C0, 0x0D0, 0x0E0, 0x0F0,
                        0x110, 0x120, 0x130, 0x140, 0x150, 0x180, 0x1A0, 0x1C0,
                        0x200, 0x210, 0x220, 0x240, 0x280, 0x2A0, 0x2C0,
                        0x300, 0x320, 0x340, 0x380, 0x3A0, 0x3C0,
                        0x400, 0x420, 0x440, 0x480, 0x4A0,
                        0x500, 0x520, 0x540, 0x580,
                        0x600, 0x620, 0x640,
                        0x700, 0x720, 0x740,
                    ]
                    for can_id in test_ids:
                        frame = f"{can_id:04x}010000000000"
                        net.send_can(frame)
                        time.sleep(0.02)  # Small delay to not flood
                    print("[SCAN] Scan complete. Check messages for valid CAN IDs.")

        # Auto-drive mode: send throttle every 100ms
        current_time = time.time()
        if auto_drive and (current_time - last_throttle_time > 0.1):
            frame = "0201016400000000"  # Full throttle
            net.send_can(frame)
            last_throttle_time = current_time

        screen.fill(BG)

        if latest:
            track = latest.get("track", {})
            half_w = float(track.get("half_width", 60.0))
            rover_x = float(latest.get("x", 0.0))
            tlx = world_to_screen_x(-half_w, half_w, rover_x)
            trx = world_to_screen_x(+half_w, half_w, rover_x)
            track_px_w = max(1, trx - tlx)
            pygame.draw.rect(screen, TRACK_CLR, (tlx, 0, track_px_w, HEIGHT), border_radius=12)

            s = float(latest.get("s", 0.0))
            length = float(track.get("length", 1.0))
            progress = s / max(1.0, length)
            if progress > 0.85:
                for y in range(0, HEIGHT, 14):
                    pygame.draw.line(screen, FINISH_CLR, (trx, y), (trx, y+7), 3)

            obs = latest.get("obstacles", [])
            if obs is not None:
                for ob in obs:
                    try:
                        dy = float(ob["dy"])
                        if dy < 0:
                            continue
                        y = int(HEIGHT/2 - dy * PX_PER_WU)
                        x = world_to_screen_x(float(ob["x"]), half_w, rover_x)
                        w_px = max(6, int((float(ob["w"]) / (2 * half_w)) * track_px_w))
                        rect = pygame.Rect(x - w_px//2, y - 10, w_px, 20)
                        pygame.draw.rect(screen, OBST_CLR, rect, border_radius=4)
                    except (KeyError, ValueError, TypeError):
                        continue

            rover_rect = pygame.Rect(WIDTH//2 - 16, HEIGHT//2 + 18, 32, 44)
            pygame.draw.rect(screen, ROVER_CLR, rover_rect, border_radius=6)
            pygame.draw.rect(screen, FG, rover_rect, 2, border_radius=6)

            hud = [
                f"Status: {latest.get('status','')}",
                f"Dist: {s:.1f}/{length:.0f} wu",
                f"Lateral x: {rover_x:.1f} wu",
                f"Speed: {float(latest.get('vel',0.0)):.2f} wu/s",
                f"Throttle: {latest.get('throttle_pct',0)}%  Steer: {latest.get('steer_pct',0)}%",
                f"{str(latest.get('msg',''))[:64]}",
            ]
            for i, line in enumerate(hud):
                img = font.render(line, True, FG)
                screen.blit(img, (12, 10 + i*18))
            
            # Add controls help
            help_text = [
                "Controls:",
                "UP: Throttle 100%  DOWN: Coast",
                "LEFT/RIGHT: Steer slight",
                "A/D: Steer hard",
                "B: Emergency brake  S: Stop",
                "R: Reset  SPACE: Auto-drive",
                "1: Scan  Q/ESC: Quit"
            ]
            if auto_drive:
                help_text.insert(1, ">>> AUTO-DRIVE: ON <<<")
            for i, line in enumerate(help_text):
                img = font.render(line, True, (150, 150, 150))
                screen.blit(img, (WIDTH - 250, 10 + i*18))

        if flag:
            img = big.render(flag, True, (255, 240, 120))
            screen.blit(img, img.get_rect(center=(WIDTH//2, HEIGHT//2 - 80)))
            if time.time() - flag_time > 6:
                pygame.quit(); return

        pygame.display.flip()
        clock.tick(60)

if __name__ == "__main__":
    main()
```


ゴールしたら画面にフラグが出た。

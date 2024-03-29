# the_game_of_life

This is an implementation of [Conway's game of](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) life in [Funk](https://github.com/diegovalverde/funk).
<p align="center">
    <img src="screenshots/pentadecatlon.gif">
    <img src="screenshots/pulsar.gif">
    <img src="screenshots/toad.gif">
    <img src="screenshots/simple.gif">
</p>
To build:

Make sure you get the Funk toolchain from [here](https://github.com/diegovalverde/funk).

```python <path_to_your_funk>/funky.py game_of_life.f --include <path_to_your_funk>/include```

Also, you may need to install [simple 2d library](https://github.com/simple2d/simple2d)

The code to generate the examples looks like this:

```perl
use nth, idem, s2d, render_board, len

# Any live cell with fewer than two live neighbours dies
update_cell(alive, cnt |  alive = 1 /\ cnt < 2 ): 0.
# Any live cell with two or three live neighbours lives
update_cell(alive, cnt | alive = 1 /\ cnt = 2 ): 1.
update_cell(alive, cnt | alive = 1 /\ cnt = 3 ): 1.
# Any live cell with more than three live neighbours dies
update_cell(alive, cnt | alive = 1 /\ cnt > 3 ): 0.
# Any dead cell with exactly three live neighbours becomes a live cell
update_cell(alive, cnt | alive = 0 /\ cnt = 3 ): 1.
# All others: ie. dead cells with no neighbours remain dead
update_cell(_, _ ): 0.
# Board has been traversed, all done
update_board(_, _, _, k, w, h | k = (w*h)): [].
# Special condition for the last few cells
update_board(tr, mr, br, k, w, h | k >= (w*(h-1))):
    0 ~>[ update_board(tr, mr, br, k+1, w, h)].
# ignore first colums as well as first and last rows
update_board(tr, mr, br, k, w, h | (k / w) = 0 \/ k % w = 0 \/ (k / w) = (w-1) ):
    0 ~>[ update_board(tr, mr, br, k + 1, w, h)].
 # ignore the last column
 update_board(a <~ [tr], b <~ [mr], c <~ [br],k ,w, h| k % w = (w-1)):
    0 ~>[ update_board(nth(tr,1), nth(mr,1), nth(br,1), k+1, w, h)].
 # count neighbours around cell 'mr' and move sliding rectangle to the left
 update_board(a <~ [tr], b <~ [mr], c <~ [br],k,w,h):
    cnt <- a + b + c + br + tr + nth(tr,1) + nth(mr, 1) + nth(br,1)
    update_cell(mr, cnt) ~> [update_board( tr, mr, br, k+1, w, h )].

s2d_render(board):
    w <- 50
    h <- 30
    next_board <-  update_board(board, nth(board,w), nth(board,2*w), 0, w, h)
    render_board(next_board, 150, 100, w, 10 )
    sleep(1)
    s2d_render(next_board).

main():
    board <- fread_list('patterns/pulsar.txt')
    s2d_window('the game of life', 800, 600 )
    s2d_render(board).

```

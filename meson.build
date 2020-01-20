project('brainfuck', 'c')

## Read the brainfuck program source from a build-option
src = get_option('src')
if src == ''
  error('No brainfuck program provided, please use -Dsrc=<YOUR-BRAINFUCK-CODE>')
endif

## Interpretor state
mem = {} ## Using dict because we can override cells easily and don't have to allocate a giant array
pc = 0
ptr = 0
loop_start = -1
loop_depth = 0
loop_locations = {} ## map depth => pc, effectively a stack implemented with a dict
skip_to_end_of_loop = false
skip_to_end_of_loop_depth = 0
output_str = ''

## Variables for converting our source string into an array
src_convert = true
src_partial = ''
src_array = []

## ASCII Table to convert integers to ASCII chars
ASCII = [
  ## First 32 are control chars and are omitted
  ' ', '!', '"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',
  '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', ':', ';', '<', '=', '>', '?',
  '@', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O',
  'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', '[', '\\', ']', '^', '_',
  '`', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o',
  'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', '{', '|', '}', '~', '?',
]

## Brainfuck ops
Ops = ['+', '-', '<', '>', '[', ']', '.', ',']

## Loop over 2^64 elements, this is effectively an infinite loop
## Suppose each loop takes 1 clock-cycle on a machine running at 3GHz
## Each loop takes .333E-9 seconds
## Thus the entire loop would take 2^64 * .333E-9 = 6142765776.54528 seconds
## Which is 6142765776.54528 / 60 / 60 / 24 / 365 = 194.78 years
## The computer this is running on probably won't even work in 50 years
## So effectively this is an infinite loop and we constructed it with only a 256 element array

_loop_arr = [0, 0, 0, 0, 0, 0, 0, 0] ## 2^3 = 8
_loop_arr += _loop_arr               ## 2^4 = 16
_loop_arr += _loop_arr               ## 2^5 = 32
_loop_arr += _loop_arr               ## 2^6 = 64
_loop_arr += _loop_arr               ## 2^7 = 128
_loop_arr += _loop_arr               ## 2^8 = 256

## Variable to help us break from multiple layers of loops
_loop_break = false

foreach _ : _loop_arr
  foreach _ : _loop_arr
    foreach _ : _loop_arr
      foreach _ : _loop_arr

        ## Accessing each char in meson is annoying since there isn't really a good way
        ## Calling .split('') aborts with an error, and there is no other obvious method
        ## Now we could just decide to require space in the input (e.g. '[ - > + < ]')
        ## Then we could call .split() to produce an opcode array, however this would require
        ## an annoying pre-processing step on the input.

        ## Since writing a brainfuck implementation in a build system lanaguage is
        ## by definition an exercise in insanity and frivolity, let's just go all
        ## the way!

        ## In any normal language this would be a function call to do a 'string => array'
        ## conversion, but meson doesn't have user-defined functions and our method requires
        ## an unbounded loop, so we abuse the loop we already have.

        if src_convert
          ## Quadratic time convertion: O(len(Ops)*len(src))
          # Luckily len(Ops) is tiny
          found = false
          foreach op : Ops
            s = src_partial + op
            if src.startswith(s)
              src_array += [op]
              src_partial = s
              found = true
              break
            endif
          endforeach
          if not found
            error('Invalid op in the input source "'+src+'" at character '+(src_array.length()+1).to_string())
          endif
          if src == src_partial
            src_convert = false
          endif
        endif

        ##################################
        ## Real interpretor loop

        ## while loop condition
        if pc >= src_array.length()
           _loop_break = true
           break
         endif

        ## while loop body
        instr = src_array[pc]
        pc += 1

        if skip_to_end_of_loop
          if instr == '['
            loop_depth += 1
          elif instr == ']'
            if loop_depth == skip_to_end_of_loop_depth
              # All Done!
              skip_to_end_of_loop = false
            endif
            loop_depth += -1
          endif
          continue
        endif

        if false

        elif instr == '>'  ## ptr++
          ptr += 1

        elif instr == '<'  ## ptr--
          ptr += -1

        elif instr == '+'  ## ++*ptr
          key = ptr.to_string()
          val = mem.get(key, 0)
          val += 1
          mem += {key: val}

        elif instr == '-'  ## --*ptr
          key = ptr.to_string()
          val = mem.get(key, 0)
          val += -1
          mem += {key: val}

        elif instr == '.'  ##  output(*ptr)
          key = ptr.to_string()
          val = mem.get(key, 0)
          c = '?' ## Print '?' for out of range values
          if 32 <= val and val < 128
            c = ASCII[val-32]
          elif val == 10
            c = '\n'
          endif

          if c != '\n'
            output_str += c
          else
            message(output_str)
            output_str = ''
          endif

        elif instr == ','  ## *ptr = input()
          error('Input operation is unimplemented')

        elif instr == '[' ## while (*ptr) {
          loop_depth += 1
          key = ptr.to_string()
          val = mem.get(key, 0)
          if val == 0
            skip_to_end_of_loop = true
            skip_to_end_of_loop_depth = loop_depth
          else
            # record the pc of this instruction
            loop_locations += {loop_depth.to_string(): pc-1}
          endif

        elif instr == ']' ## <end-of-while>
          pc = loop_locations[loop_depth.to_string()]
          loop_depth += -1

        else
          error('Unknown brainfuck instruction: ' + instr)
        endif

      endforeach
      if _loop_break
        break
      endif
    endforeach
    if _loop_break
      break
    endif
  endforeach
  if _loop_break
    break
  endif
endforeach

if output_str != ''
  message(output_str)
endif

message('Final machine state:')
message(mem)

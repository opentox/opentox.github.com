---
layout: post
title: "Ruby: Parallel collect()"
description: "How to parallelize ruby's collect() method"
category: "ruby"
tags: [ruby, parallel processing]
---
{% include JB/setup %}

**How to parallelize ruby's collect() method**

Ruby's Array::collect() method is easily parallelized (call it `pcollect`):

    class Array
      # collect method extended for parallel processing.
      # Note: assign return value as: ans = arr.pcollect(n) { |obj| ... }
      # @param n the number of processes to spawn (default: unlimited)
      def pcollect(n = nil)
        nproc = 0
        result = collect do |*a|
          r, w = IO.pipe
          fork do
            r.close
            w.write( Marshal.dump( yield(*a) ) )
          end
          if n and (nproc+=1) >= n
            Process.wait ; nproc -= 1
          end
          [ w.close, r ].last
        end
        Process.waitall
        result.collect{|r| Marshal.load [ r.read, r.close ].first}
      end
    end

It uses pipe messaging between parent and children. Importantly, **the input array order is preserved** in the output.

Parameter `n` takes the number of children to run in parallel. The default is to start all children simulataneously, which is **not recommended**.

Example from `last-utils/lu.rb`, which I have parallelized to match SMARTS patterns on molecules more efficiently:

    # Match SMARTS expressions on (molecules given by) SMILES.
    # smiles: array of SMILES strings
    # smarts: array of SMARTS strings
    # hit_count: whether to count the occurrences of each SMARTS in each SMILES
    def match_rb (smiles,smarts,hit_count=false) 
      smarts_mc = smarts.pcollect(4) do |s| # use 4 parallel children
        (1...smiles.length).to_a.collect do |id|
          match_res=match(smiles[id],s,false,hit_count)
          [...]
        end
      end
    end

It is important to parallelize the **outer loop**, not the inner one, since the overhead for tracking the forked children is quite high.

Use `smarts_mc = smarts.collect do |s|` to revert to the old (non-parallel) version and compare running times!

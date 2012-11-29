---
layout: post
title: "Ruby hash 的魔力"
category:
tags: []
---
{% include JB/setup %}

example 1:

	fibonacci = Hash.new { |numbers, index|
	    numbers[index] = fibonacci[index - 2] + fibonacci[index - 1]
	  }.update(0 => 0, 1 => 1)
	p fibonacci[300]
	#=> 222232244629420445529739893461909967206666939096499764990979600


example 2:

	deep = Hash.new { |hash, key| hash[key] = Hash.new(&hash.default_proc) }
	deep[:a][:b][:c] = 42
	p deep
	#=> {:a=>{:b=>{:c=>42}}}

example 3:

    votes = { "Josh"  => %w[SBPP  POODR GOOS],
              "Avdi"  => %w[POODR SBPP  GOOS],
              "James" => %w[POODR GOOS  SBPP],
              "David" => %w[GOOS  SBPP  POODR],
              "Chuck" => %w[GOOS  POODR SBPP] }
	tally = Hash.new(0)
	votes.values.each do |personal_selections|
	  personal_selections.each_with_object(tally).with_index do |(vote, totals), i|
		totals[vote] += personal_selections.size - i
	  end
	end
	p tally
	#=>{"SBBPP"=>9, "POODR"=>11, "GOOS"=>10}
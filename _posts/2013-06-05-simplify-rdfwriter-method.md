---
layout: post
title: "simplify RDF::Writer method"
description: ""
category: "Development" 
tags: ["RDF"]
---
{% include JB/setup %}

Simplify the RDF::Writer method if you have already an rdf graph like:

    @rdf = RDF::Graph.new
    RDF::Reader.for(format).new(rdf) do |reader|
      reader.each_statement{ |statement| @rdf << statement }
    end

To parse it to another format simply use:

    RDF::Writer.for(format).buffer(:encoding => Encoding::ASCII) do |writer|
      writer << @rdf
    end

instead of:

    RDF::Writer.for(format).buffer(:encoding => Encoding::ASCII) do |writer|
      @rdf.each{|statement| writer << statement}
    end

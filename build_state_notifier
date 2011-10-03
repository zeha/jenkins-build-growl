#!/usr/bin/env ruby

require 'rexml/document'
require 'net/http'
require 'yaml'

STORAGE = '.buildinfocache'
INTERVAL = 30
JENKINS_HOST = ARGV.shift
USE_SSL = ARGV.shift == '--no-ssl' ? false : true

def run
  datastore = YAML.load(File.open(STORAGE)) rescue {}

  docbody = nil
  Net::HTTP.start(JENKINS_HOST, {:use_ssl => USE_SSL}) {|http|
    req = Net::HTTP::Get.new('/rssLatest')
    #req.basic_auth 'account', 'password'
    response = http.request(req)
    docbody = response.body
  }
  doc = REXML::Document.new docbody
  doc.elements["feed"].elements.each do |e|
    next if e.name != "entry"
    entry = {}
    e.elements.each do |el|
      entry[el.name.to_sym] = el.text
      if el.name == "title"
        m = el.text.match(/^(.+) #(\d+) \((.+)\)$/)
        entry[:project] = m[1]
        entry[:build] = m[2]
        entry[:state] = m[3]
      end
    end
    old = datastore[entry[:project]]
    if not old.nil?
      if old[:state] != entry[:state]
        `growlnotify -m "#{entry[:state]}" "#{entry[:title]}"`
      end
    end
    datastore[entry[:project]] = entry
  end

  File.open(STORAGE, 'w') do |f|
    YAML.dump(datastore, f)
  end
rescue => e
  puts e.inspect
end

while true
  run
  sleep 30
end

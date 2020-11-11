require 'fileutils'
require 'pathname'
require 'find'

def plantuml

end

def is_root?(filename)
    %r{// ROOT} =~ IO.readlines(filename).first
end

def find_root_asciidocs_files
    Find.find('.').select do |filename|
        filename.end_with? '.asciidoc' and is_root? filename
    end
end

def compile_asciidoc
    puts "Compiling asciidocs..."
    roots = find_root_asciidocs_files
    `asciidoctor -R docs -D dist #{roots.join(' ')}`
end

def should_copy?(path)
    false
end

def copy_images
    root = Pathname.new 'docs'
    dist = Pathname.new 'dist'

    Find.find('docs') do |entry|
        if should_copy? entry
            path = Pathname.new entry
            relative_path = path.relative_path_from(root)
            from_path = root + relative_path
            to_path = dist + relative_path

            puts "Copying #{from_path} to #{to_path}"
            FileUtils.copy(from_path.to_s, to_path.to_s)
        end
    end
end

def compile_graphviz
    root = Pathname.new 'docs'
    dist = Pathname.new 'dist'

    Find.find('docs') do |entry|
        if entry.end_with? '.gv'
            path = Pathname.new entry
            relative_path = path.relative_path_from root
            from_path = root + relative_path
            to_path = dist + relative_path

            puts "#{from_path} -> #{to_path}"
            output = `dot -Tsvg #{from_path.expand_path} -o #{to_path.expand_path.sub_ext('.svg')}`.strip
            puts output if output.size > 0
        end
    end
end

task :clean do
    FileUtils.rm_rf 'dist'
end

task :build do
    compile_asciidoc
    compile_graphviz
    #copy_images
end

task :upload do
    Dir.chdir 'dist' do
        `ssh -p 22345 -l upload leone.ucll.be rm -rf /home/frederic/courses/vgo/volume/*`
        puts `scp -P 22345 -r * upload@leone.ucll.be:/home/frederic/courses/vgo/volume`
    end
end

task :default => [ :build ]
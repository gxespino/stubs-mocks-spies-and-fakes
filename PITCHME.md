### Stubs, Mocks, Spies, Fakes and Doubles
#### (Nothing is real)

---

### TIL:

- Stubs
- Mocks
- Spies
- Fakes
- Doubles

---

### Background

+++?code=src/background.rb&lang=ruby

---

### Stubs

```ruby
describe '#process' do
  it 'returns a successful Result object' do
    processor = Processor.new

    result = processor.process('test_file.csv')

    expect(result.successful?).to eq(true)
  end
end
```

##### No stubs in place. Object and it's tests are tightly _coupled_ to it's dependencies

+++

```ruby
describe '#process' do
  it 'returns a successful Result object' do
    processor  = Processor.new
    parsed     = double('parsed')
    loaded     = double('loaded')
    allow(Parser).to receive(:parse).and_return(parsed)
    allow(Loader).to receive(:load!).and_return(loaded)

    result = processor.process('test_file.csv')

    expect(result.successful?).to eq(true)
  end
end
```

#### In this example we are dictating what Parser and Loader will return, decoupling Processor from the implementation details of it's collaborators. Parser and Loader can change, but Processor's tests won't and shouldn't break because they should only be concerned with how Processor works.

---

### Mocks

---

### Spies

---

### Fakes

---

### Doubles

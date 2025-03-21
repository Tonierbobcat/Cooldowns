# Cooldowns

```java
/**
 * @Author Logan
 * @Github https://github.com/Tonierbobcat
 */

public interface Cooldown {

    /**
     * @return {@code true} if {@code UUID} has cooldown
     */
    boolean has(UUID uuid);

    void set(UUID uuid);
    
}
```

```java
/**
 * @Author Logan
 * @Github https://github.com/Tonierbobcat
 */

public class ComplexCooldown implements Cooldown {
    private final ConcurrentHashMap<UUID, Long> cooldowns = new ConcurrentHashMap<>();
    private final long interval;
    public ComplexCooldown(long intervalMillis) {
        this.interval = intervalMillis;
    }


    @Override
    public boolean has(UUID uuid) {
        Long last = cooldowns.get(uuid);
        if (last == null) {
            return false;
        }
        if ((System.currentTimeMillis() - last) >= interval) {
            cooldowns.remove(uuid);
            return false;
        }
        return true;
    }

    @Override
    public void set(UUID uuid) {
        cooldowns.put(uuid, System.currentTimeMillis());
    }
}
```

```java
/**
 * @Author Logan
 * @Github https://github.com/Tonierbobcat
 */

public class SimpleCooldown extends ComplexCooldown {
    public SimpleCooldown(long intervalMillis) {
        super(intervalMillis);
    }
    
    @Override
    public boolean has(UUID uuid) {
        var has = super.has(uuid);
        if (!has)
            set(uuid);
        return has;
    }
}
```
